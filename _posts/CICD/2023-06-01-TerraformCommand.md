---
title: 테라폼 명령어(Terraform Command)
author: doombtter
date: 2023-06-10
category: cicd
layout: post
---


# Terraform Command
- 테라폼(터미널)에서 사용 할 수 있는 명령어의 집합입니다.

## 자주 쓰이는 명령어

``` sh
terraform init : 환경을 초기화시킵니다
```

- 이는 현재 디렉토리의 구성 파일을 읽고 필요한 Provider Plugin을 다운로드하고 설치하는 것을 의미합니다.

``` sh
terraform plan : 상태를 적용시킨다면 어떤 결과가 발생할지 미리 확인합니다.
```

- 이는 변경 예상 사항을 비교해서 보고하거나, 추가 / 변경 / 삭제될 리소스를 확인하거나 하는 작업을 미리 출력 받아 볼  수 있습니다.

``` sh
terraform apply : 상태를 적용시킵니다.
```

- 이는 'terraform plan'으로 미리 본 변경사항을 적용시키고 Infrastructure을 Provisioning합니다.

``` sh
terraform destroy : 모든 상태를 파괴(삭제)합니다.
```

- 이는 모든 리소스를 파괴하여 Infrastructure를 비활성화 하기에 주의해야합니다.

``` sh
terraform validate : 구성 파일에 오류가 있는지 검사합니다.
```

- 이는 구성 파일에 오타가 있는지 등을 검사하여 실행 시 발생하는 오류를 미연에 방지하거나, 구성 파일의 유효성을 검사합니다.

## 유용한 명령어

``` sh
terraform graph : 구성 파일에서 정의된 Infrastructure 리소스 간의 종속성을 시각화하는 그래프를 생성합니다
```

- 출력 예
``` sh
digraph {
        compound = "true"
        newrank = "true"
        subgraph "root" {
                "[root] var.AMIS" [label = "var.AMIS", shape = "note"]
                "[root] var.PATH_TO_PRIVATE_KEY" [label = "var.PATH_TO_PRIVATE_KEY", shape = "note"]
                "[root] aws_instance.example (expand)" -> "[root] var.AMIS"
                "[root] root" -> "[root] var.PATH_TO_PRIVATE_KEY"
                ...
        }
}
```

``` sh
terraform fmt : 구성 파일의 포맷을 일관되게 정렬합니다.
```

- 이는 자동으로 일관된 간격과 들여쓰기를 하게 하여 가독성을 높이는 효과가 있습니다.

``` sh
terraform output : 출력 변수의 값을 표시합니다.
```

- 이는 instance의 ip나 id를 확인하는데에 유용합니다.

``` sh
terraform refresh : Infrastructure를 상태 파일에 기반하여 업데이트합니다.
```

- 이는 실제 Infrastructure의 현재 상태와 상태 파일을 비교하여 변경 사항을 감지하기에 유용하게 사용될 수 있습니다.

``` sh
terraform get : 모듈이나 외부 모듈의 종속성을 다운로드하고 업데이트합니다.
```

- 이는 로컬 모듈이나 외부 모듈(원격 저장소) 에서 필요한 종속성을 가져옵니다.

``` sh
terraform import : 이미 존재하는 Infrastructure을 테라폼 상태 파일로 가져옵니다.
```

- 이는 이미 생성된 instance등을 테라폼으로 가져와서 관리하는 것에 유용합니다.

``` sh
terraform taint : 리소스를 마킹하여 '오염된' 상태로 표시합니다.
terraform untaint : '오염된' 상태를 취소합니다.
```

- 이는 다음 Terraform 실행 시에 '오염된' 리소스를 강제로 다시 생성합니다.

- 다른 추가적인 명령어들은 공식 문서에서 확인 할 수 있습니다.


## Terraform workspace
-  workspace는 git의 branch와 유사한 성격을 가지고 있습니다.
- 자주 쓰이는 명령어는 아래와 같습니다.

``` sh
terraform workspace new [name] : 워크스페이스 생성
terraform workspace select [name] : 워크스페이스 전환
terraform workspace list [name] : 워크스페이스의 리스트 출력
terraform workspace delete [name] : 워크스페이스 삭제
```

- 이외의 명령어는 아래와 같습니다.

``` sh
terraform workspace show : 현재 워크스페이스 출력
terraform workspace copy [from] [to] : 선택한 워크스페이스의 모든 상태를 다른 워크스페이스로 복사합니다.
```

- 다른 추가적인 명령어들은 공식 문서에서 확인 할 수 있습니다.

## Terraform state
- state 명령어는 상태 파일에 저장된 Infrastructure 상태를 관리하는 것에 사용됩니다.

``` sh
terraform state list : 상태 파일에 정의된 모든 리소스의 리스트를 출력합니다.
terraform state show [resource] : 특정 리소스의 세부 정보를 출력합니다.
terraform state rm [resource] : 특정 리소스를 상태 파일에서 제거합니다.

terraform state pull : 현재 구성된 인프라 리소스의 상태 정보를 로컬로 가져옵니다, '.tfstate'  확장자를 가지며 편집이 가능합니다.
terraform state push : 로컬에서 변경된 상태 파일을 원격 상태 스토리지에 업데이트 합니다.
```

## 참고 문서
- [Basic CLI Features - HashiCorp][1]

[1]: https://developer.hashicorp.com/terraform/cli/commands