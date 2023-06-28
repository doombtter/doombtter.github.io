---
title: Git Webhook을 이용한 웹사이트 AWS Serverless 자동화 배포
author: doombtter
date: 2023-06-10
category: cicd
layout: post
---


# 방식 소개
- 배포에 사용한 방식은 Blue-Green 배포 방식입니다. Blue-Green 배포는 새로운 버전의 애플리케이션을 기존 버전과 별도의 환경에 배포하고, 트래픽을 이 두 가지 환경 사이에서 전환하는 방식입니다.
- 과정은 이러합니다. GitHub Webhook을 사용하여 변경 사항이 발생하면 AWS Lambda 함수를 실행하고, 해당 함수에서 S3에 웹사이트 파일을 업로드하여 배포하는 것입니다. 이를 통해 새로운 버전의 웹사이트를 Blue(새로운 버전) 환경에 배포하고, 기존 버전인 Green(이전 버전)과의 전환을 수행할 수 있습니다.
- 서버리스 방식으로 서버를 구축 할 필요 없이, 여러 클라우드 서비스에서 제공하는 서버 인프라를 제공하여 실행되는 함수 실행 시간, 메모리 사용량등의 지표로 과금이 이루어집니다.

## 전제 조건
- AWS에 회원가입이 되어 있어야 합니다.
- [AWS 공식 사이트][1] 를 통해 회원 가입이 가능합니다.
- Github 액세스 토큰이 활성화되어 있어야 합니다.

## 실습

### 1. AWS S3 버킷 구성

- AWS Management Console에서 S3 서비스로 이동합니다.
- 새 버킷을 생성하고 이를 public으로 전환합니다.
- 버킷의 [속성] 탭에서 정적 웹 사이트 호스팅을 활성화합니다.

### 2. AWS API Gateway 설정
- AWS Management Console에서 API Gateway 서비스로 이동합니다.
- 그림에 보이는 REST API [구축] 버튼을 누릅니다
- ![webhook1]({{"/assets/webhook/webhook1.png"| relative_url}})
- 그림과 같이 API 이름과 엔드포인트 유형을 지정해줍니다.
- 최적화된 Edge API는 CloudFront 네트워크에 배포됩니다. 지역, private은 각각 해당 리전과 VPC내에서만 사용할 수 있는 API를 구축합니다.
- 예제에선 Edge API를 사용하지만 배포 API는 모범 보안 사례로 private api를 사용 하는 것이 좋으며, 이렇게 구축된 API는 외부에서 호출이 불가능하고 VPC내에서만 호출 가능합니다.
- ![webhook2]({{"/assets/webhook/webhook2.png"| relative_url}})
- 아래 사진과 같이 POST 메서드를 생성해주고, Lambda 함수와 통합합니다. 3번으로 이동하여 Lambda 함수를 구성하고 돌아옵니다.
- ![webhook3]({{"/assets/webhook/webhook3.png"| relative_url}})
- 함수 작성을 완료했다면 API Gateway로 돌아와 Lambda 함수의 이름을 작성하고 메소드 구성을 완료합니다.
- POST 메소드를 클릭하고 [작업] 버튼을 누른 뒤에 [CORS활성화], [API 배포] 작업을 각각 수행해줍니다. 완료되면 Endpoint를 기억해둡니다.

### 3. AWS Lambda 함수 작성
- AWS Management Console에서 Lambda 서비스로 이동합니다.
- [함수 생성] 버튼을 눌러 함수 이름과 런타임을 구성해줍니다. Node.js를 사용합니다.
- 아래 코드를 입력합니다. 'bucketName'과 'localWebsitePath'는 해당 프로젝트에 맞게 입력해줍니다.

```javascript
import fs from 'fs';
import path from 'path';
import axios from 'axios';
import AWS from 'aws-sdk';

export async function handler(event) {
  try {
    // GitHub 웹훅 이벤트 데이터 가져오기
    const eventData = JSON.parse(JSON.stringify(event));

    // 필요한 데이터 추출
    const repositoryName = eventData.repository.name;
    const repositoryUrl = eventData.repository.html_url;
    const commitId = eventData.after;

    // 웹사이트 배포 로직 작성
    const bucketName = 'YOUR_BUCKET_NAME';
    const specificFolder = 'BUILD_FOLDER_PATH';

    await uploadWebsiteFolderToS3(bucketName, specificFolder);

    // 배포 완료 메시지 출력
    console.log(`Website deployed for repository: ${repositoryName}`);
    console.log(`Commit ID: ${commitId}`);
    console.log(`Website URL: ${repositoryUrl}`);

    return {
      statusCode: 200,
      body: 'Website deployed successfully'
    };
  } catch (error) {
    console.error('Error deploying website:', error);
    return {
      statusCode: 500,
      body: 'Error deploying website'
    };
  }
}

// 특정 폴더 내의 파일들을 S3에 업로드하는 함수
async function uploadWebsiteFolderToS3(bucketName, specificFolder) {
  const fileList = await listFiles(specificFolder);

  // 파일을 순회하며 S3에 업로드
  for (const file of fileList) {
    const fileContent = fs.readFileSync(file.path);

    const s3Params = {
      Bucket: bucketName,
      Key: file.relativePath,
      Body: fileContent
    };

    // AWS.S3 객체를 직접 사용
    await new AWS.S3().putObject(s3Params).promise();
  }
}

// 특정 폴더 내의 파일 목록을 가져오는 함수
async function listFiles(specificFolder) {
  const fileList = [];

  // GitHub API를 사용하여 특정 폴더의 파일 목록 가져오기
  const apiUrl = `https://api.github.com/repos/{owner}/{repo}/contents/${specificFolder}`;
  const githubAccessToken = 'YOUR_GITHUB_ACCESS_TOKEN'; // GitHub 액세스 토큰을 설정해야 합니다

  const options = {
    headers: {
      'User-Agent': 'AWS Lambda',
      'Authorization': `Bearer ${githubAccessToken}`
    }
  };

  const response = await axios.get(apiUrl, options);
  const items = response.data;

  // 각 파일에 대해 로컬 경로와 상대 경로를 설정하고 목록에 추가
  for (const item of items) {
    if (item.type === 'file') {
      const fileUrl = item.download_url;
      const fileName = path.basename(fileUrl);
      const fileRelativePath = path.join(specificFolder, fileName);
      const localFilePath = path.join(tempDir, fileName);

    const fileResponse = await axios.get(fileUrl, { responseType: 'arraybuffer' });
    await fs.promises.writeFile(localFilePath, fileResponse.data);

    fileList.push({
      path: localFilePath,
      relativePath: fileRelativePath
    });
    } else if (item.type === 'dir') {
      const subFolder = item.path;
      const subFiles = await listFiles(subFolder);

      fileList.push(...subFiles);
    }
  }

  return fileList;
}
```

- [DEPLOY] 버튼을 눌러 변경 사항을 저장해줍니다.

### 4. Github Webhook 구성
- Github repositoy로 이동합니다.
- Webhook을 활성화합니다.
- Payload URL에는 발급받은 API의 endpoint, Content type은 application/json으로 설정합니다.
- 코드를 수정하고 push를 통해 구성을 테스트합니다.

## 결론
- 규모가 작은 웹 프로젝트에선 유효할 수 있으나 timeout과 같은 문제로 규모가 커진다면 다른 방법을 채택해야 할 것 같다.

[1]: https://aws.amazon.com/ko/?nc2=h_lg