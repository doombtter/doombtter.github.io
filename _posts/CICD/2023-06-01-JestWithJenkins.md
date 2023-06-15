---
title: Jest와 Jenkins 연동
author: doombtter
date: 2023-06-10
category: cicd
layout: post
---


# Jest 소개
- Jest는 Meta에서 개발한 JavaScript Test Framework입니다.
- Node.js 애플리케이션의 단위 테스트, 통합 테스트, 스냅샷 테스트 등을 지원하며, 자체적으로 mocking 및 assertion 기능을 제공합니다.

## Jest 설치
- 설치 : 프로젝트 루트 디렉토리에서 터미널을 열고 하단 스크립트를 입력합니다.
``` sh
npm install --save-dev jest
``` 
- 테스트 파일 작성 : Jest는 프로젝트에서 모든 '.test.js' 확장자를 가진 파일을 자동으로 찾아 실행합니다.
```js
test('', () => {});
```
- 위와 같은 형식의 테스트 스크립트를 작성할 수 있으며,
```sh
npx jest
```
- 위와 같은 스크립트로 테스트 실행이 가능합니다.
## 연동 과정
- 사전 준비 : Jenkins 관리자 페이지로 이동하여  "Global Tool Configuration"을 엽니다.
- Node.js를 설치합니다.
- "Add NodeJS" 버튼을 클릭하여 Node.js 버전을 선택하고, 설치할 경로를 지정합니다. 설치 완료 후 "Save" 버튼을 클릭합니다.
- 기존 소스 코드 관리 구성 : 빌드 단계에서 "Execute shell"을 추가하고,  하단 스크립트를 입력합니다
``` sh
npm install
npm test
``` 
- Code Pipeline 구성 : 위 방법 외에 JenkinsFile을 작성하는 방법입니다.
``` sh
pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Install dependencies') {
      steps {
        sh 'npm install'
      }
    }
    stage('Run tests') {
      steps {
        sh 'npm test'
      }
    }
  }
}
```