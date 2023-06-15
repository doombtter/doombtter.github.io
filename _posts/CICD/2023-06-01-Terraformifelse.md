---
title: 테라폼 조건문(Terraform Conditionals)
author: doombtter
date: 2023-06-10
category: cicd
layout: post
---


# Conditionals
- Interpolations(보간법)에 포함 될 수 있음.

## 조건문 사용법
- 삼항연산자와 동일하게 "조건 ? 참 : 거짓"의 형식으로 사용
``` sh
CONDITION ? TRUE : FALSE
``` 

## 예시
``` sh
resource "aws_instance" "example" {
  ami           = var.condition ? "ami-12345678" : "ami-87654321"
  instance_type = var.condition ? "t2.micro" : "t2.small"
}
``` 

## 지원 연산자
- 다른 프로그래밍 언어와 거의 동일함
``` sh
동등 연산자  : == , !=
수치 비교 :  >. <, >=, <=
논리 연산자 : &&, ||, !
```

## 예시
``` sh
variable "num1" {
  default = 10
}

variable "num2" {
  default = 5
}

output "greater_than" {
  value = var.num1 > var.num2 #true
} 

output "equal_to" {
  value = var.num1 == var.num2 #false
}

variable "is_enabled" {
  default = true
}

output "result" {
  value = !var.is_enabled #값을 반전시킴
}
```