---
title: 메모화(Memoization)
author: doombtter
date: 2023-06-10
category: js
layout: post
---

# Memoization
 
- 메모화(Memoization)는 이전에 계산한 결과를 저장하여 계산에 중복을 피하는 프로그래밍 기법입니다. 이 기법은 실행 시간을 단축하고 성능을 향상 시킬 수 있습니다.
## 사용 이유
- 계산 비용이 큰 함수의 성능 향상
- 실행 시간 단축

## 메모화의 단점

- 불필요한 결과를 오래 저장 할 수 있어, 동일한 파라미터로 반복적인 호출이 되는 사례가 아니라면 종은 선택이 아닐 수 있다.

## 출력 예제

```javascript 
function memoizedFunction(func) {
  const cache = {};
  
  return function(...args) {
    const key = JSON.stringify(args);
    
    if (cache[key]) {
      return cache[key];
    }
    
    const result = func.apply(this, args);
    cache[key] = result;
    
    return result;
  };
}

function expensiveCalculation(n) {
  // 비용이 많이 드는 계산을 수행하는 함수
  console.log('Calculating...');
  return n * 2;
}

const memoizedCalculation = memoizedFunction(expensiveCalculation);

console.log(memoizedCalculation(5)); // 처음 호출되므로 계산 수행 후 결과 반환
console.log(memoizedCalculation(5)); // 이전에 계산된 결과를 반환하여 중복 계산 피함

``` 
