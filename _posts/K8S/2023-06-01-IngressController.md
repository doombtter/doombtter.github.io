---
title: 인그레스 컨트롤러(Ingress Controller)
author: doombtter
date: 2023-06-10
category: k8s
layout: post
---

# Ingress Controller
 
 - 클러스터로 외부에서 쉽게 접근이 가능하게하는 컴포넌트
 - 클러스터 내에서 라우팅 규칙을 정의하는 K8s 리소스
 - 직접 LB를 구축이 가능

## 작동 원리
- Ingress 리소스를 생성하여 라우팅 규칙을 정의합니다.
- Ingress Controller는 클러스터 내의 Ingress 리소스를 주기적으로 감지하거나 이벤트 기반으로 변경사항을 파악하여 실시간으로 업데이트합니다.
- Reverse Proxy 서버의 구성을 요청에 따라 어떻게 처리할지 결정합니다.
- 외부 요청을 받아들이고, 정의된 라우팅 규칙에 따라 적절한 서비스로 라우팅합니다.
- 서비스에서 처리된 요청에 대한 응답을 Ingress Controller가 클라이언트로 반환합니다.

## 작성 예제

``` yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-rules
spec:
  rules:
    - host: v1.test.com
      http:
        paths:
          - path: /
            backend:
              service:
                name: v1
                port:
                  name: http
    - host: v2.test.com
      http:
        paths:
          - path: /
            backend:
              service:
                name: v2
                port:
                  name: http
``` 
