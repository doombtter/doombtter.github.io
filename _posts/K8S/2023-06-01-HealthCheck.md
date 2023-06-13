---
title: 쿠버네티스 헬스 체크(Kubernetes Health Check)
author: doombtter
date: 2023-06-10
category: k8s
layout: post
---

## Health Check의 사용 목적?
 - 시스템, 서비스 또는 애플리케이션의 상태를 확인합니다.
 - 가용성(정상적으로 사용이 가능한 정도)을 모니터링합니다.
 - Load Balancer 사용 시에 Health Check를 통해 서버의 상태를 확인하고 정상적인 서버에만 트래픽을 전달할 수 있습니다.

## K8S에서의 Health Check
- 애플리케이션에 이상이 생겨도 Pod은 계속 작동할 수 있다.
- 이를 감지하고 해결하기 위해 Health Check를 사용한다
- Container에서 명령으로 실행이 가능하다.
- HTTP를 사용해 주기적으로 URL을 확인할 수 있다.

## Liveness Probe
- Liveness Probe는 컨테이너 내부의 애플리케이션이 여전히 실행 중인지를 확인하는 데 사용됩니다. 컨테이너가 비정상적인 상태에 빠진 경우, Liveness Probe는 해당 컨테이너를 재시작하거나 다른 조치를 취할 수 있도록 Kubernetes에 알립니다. 일반적으로 Liveness Probe는 HTTP 요청, TCP 연결, 실행 가능한 명령 등을 통해 애플리케이션의 상태를 확인합니다.

## Readiness Probe
- Readiness Probe는 컨테이너가 클라이언트의 요청을 처리할 준비가 되었는지를 확인하는 데 사용됩니다. 컨테이너가 초기화 또는 로드되는 동안 애플리케이션이 완전히 실행 준비되기 전에 요청을 처리하는 것을 방지하기 위해 사용됩니다. Readiness Probe가 실패하는 경우, 해당 컨테이너는 트래픽의 대상에서 제외될 수 있습니다. 일반적으로 Readiness Probe는 Liveness Probe와 유사한 방식으로 애플리케이션의 상태를 확인합니다.

## 구성 방식
- HTTP Probe: HTTP GET 요청을 특정 경로 또는 포트로 보내고, 특정 응답 코드 또는 응답 본문을 확인합니다.
- TCP Probe: 특정 포트로 TCP 연결을 시도합니다. 연결이 성공하면 컨테이너가 정상 작동 중이라고 판단됩니다.
- Exec Probe: 컨테이너 내에서 실행 가능한 명령을 실행하고, 명령의 종료 코드를 확인합니다.
- 스크립트 Probe: 컨테이너 내에서 사용자 정의 스크립트를 실행하고, 결과를 확인합니다.

## 각 Probe가 가지는 속성
- 초기 지연 시간 (initial delay): 컨테이너가 시작된 후 Probe를 시작하기까지 대기하는 시간입니다.
- 주기 (period): Probe를 실행하는 주기입니다.
- 타임아웃 시간 (timeout): Probe의 응답을 기다리는 시간입니다.
- 실패 임계값 (failure threshold): Probe가 연속으로 실패한 경우 컨테이너를 비정상적인 상태로 간주합니다.
- 성공 임계값 (success threshold): Probe가 연속으로 성공한 경우 컨테이너를 정상적인 상태로 간주합니다.

## 작성 예제

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: health-check-test
spec:
  replicas: 3
  selector:
    matchLabels:
      app: test
  template:
    metadata:
      labels:
        app: test
    spec:
      containers:
      - name: health-check-test-container
        image: me/health-check-test-container
        ports:
        - name: test-port
          containerPort: 3000
        livenessProbe:
          httpGet:
            path: /
            port: nodejs-port
          initialDelaySeconds: 15
          timeoutSeconds: 30
        readinessProbe:
          httpGet:
            path: /
            port: test-port
          initialDelaySeconds: 15
          timeoutSeconds: 30

``` 