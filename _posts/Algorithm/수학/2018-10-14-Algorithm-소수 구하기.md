---
title: "[Algorithm]소수 구하기"
excerpt: "에라토스테네스의 체"
search: true
categories:
  - Algorithm
tags:
  - 수학
toc: true
---

# 소수

## 에라토스테네스의 체

### 정의
- 제일 작은 소수 2부터 N-1까지 수 중에서 2의 배수를 모두 체로 거르고, 남은 수중에서 3의 배수를 거르는 과정을 반복하여 루트N 까지 걸러서 남은 수들이 이 범위안의 소수이다.

### 구현

~~~cpp
//에라토스테네스의 체
prime[0] = prime[1] = 1;  //0,1은 소수 판별에서 제외되는 숫자이기 때문
for (int i = 2; i < N; i++) {
	if (prime[i] == 0) {
	       for (int j = i + i; j < N; j += i)
	                prime[j] = 1;
	}
}  //prime 배열의 값이 0인 인덱스가 소수이다.
~~~

- 일정 범위 내의 수에서 모든 소수를 구하는 문제에 주로 사용된다.
