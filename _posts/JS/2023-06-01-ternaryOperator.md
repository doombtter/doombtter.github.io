---
title: 삼항 조건 연산자(Conditional (ternary) operator)를 적재적소에 사용하기
author: doombtter
date: 2023-06-10
category: js
layout: post
---

# 삼항 조건 연산자(Conditional (ternary) operator)

- 조건 뒤에 실행할 참과 거짓을 나열하는 if...else 조건문의 대안
- 코드를 간결하게 하고 가독성을 향상시키는 효과를 가짐

## 사용법

- (조건) ? 참 : 거짓의 형태로 이루어짐
- 아래는 삼항연산자의 몇가지 예시입니다.

``` javascript
// 변수에 값을 대입하는 삼항연산자
var variable = (conditional) ? true : false
// 조건에 따라 반환값이 달라지게 하는 삼항연산자
return (conditional) ? true : false
// 조건이 연속된 중첩 삼항연산자
return (first_conditional) ? (second_conditional) ? true : false : false
```

## 모범 사례

1. 간단한 조건문일때는 유용하게 사용 될 수 있습니다. 코드를 간결하게 하여 가독성을 향상시킵니다.
2. 조건에 따라 반환값이 달라져야 할 때 유용하게 사용됩니다.

## 좋지 않은 사례

1. 복잡한 조건문일때는 오히려 가독성이 저하될 수 있습니다.
2. 표현식 내부에서 변수의 값을 변경하거나 함수를 호출하여 외부 상태에 영향을 주는 경우엔 if...else를 사용하는 것이 권장됩니다.
- 예시 :

``` javascript
int x = 5;
int y = (x > 0) ? x++ : x--;
```
3. 여러 개의 조건을 처리해야 하는 경우, 상황에 따라 가독성이 저하 될 수 있습니다.