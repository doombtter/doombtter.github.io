---
title: 제너레이터(Generators)
author: doombtter
date: 2023-06-10
category: js
layout: post
---

# Generators
 

- 제너레이터(Generators)는 함수의 실행을 일시 중지하고 나중에 다시 시작할 수 있는 함수입니다.
값을 한번에 하나씩 반환하고 관리가 가능한 반복 가능한 개체를 생성합니다.

## 사용 이유
- 비동기 작업을 동기적인 스타일로 사용이 가능
- 중단과 재개로 여러 단계로 나누어 처리할때 유용
- Lazy evaluation으로 필요한 만큼의 값을 생성하고 반환하여 메모리 절약에 유리

## 제너레이터의 단점

- 반복문과 같은 간단한 작업에는 일반적인 함수보다 느릴 수 있음
- 오류 처리가 일반적인 함수보다 어려울 수 있음

## 사용법

```javascript
function* functionName() {} // 을 사용하여 정의
yield // 키워드를 만날 때 마다 일시 중지
```

## 출력 예제

```javascript 
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const generator = numberGenerator();

console.log(generator.next()); // { value: 1, done: false }
console.log(generator.next()); // { value: 2, done: false }
console.log(generator.next()); // { value: 3, done: false }
console.log(generator.next()); // { value: undefined, done: true }

``` 
