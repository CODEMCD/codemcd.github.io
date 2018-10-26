---
title: "[ALGOSPOT]SNAIL"
excerpt: "DP"
search: true
sidebar_main: true
categories:
  - PS
tags:
  - Algospot
---

# 장마가 찾아왔다

## 문제 링크
- [https://algospot.com/judge/problem/read/SNAIL](https://algospot.com/judge/problem/read/SNAIL)

## 코드 분석
**동적 계획법을 활용한 확률 계산**
- 예제 문제: 우물의 깊이 n미터, 장마 기간 m일, 비가올 확률 50%, 달팽이는 비가 안올 때 1m 올라가고 비가 올 때 2m를 올라간다. 이때, 올라갈 수 있는
확률을 구하라.

```cpp
int cache[N][2 * N + 1];
int n, m;
//달팽이가 days일 동안 climbed미터를 기어올라 왔다고 할 때,
//m일 전까지 n미터를 기어올라갈 수 있는 경우의 수
int climb(int days, int climbed)
{
	//기저 사례: m일이 모두 지난 경우
	if (days == m) return climbed >= n ? 1 : 0;
	//메모이제이션
	int &ret = cache[days][climbed];
	if (ret != -1) return ret;
	return ret = climb(days + 1, climbed + 2) + climb(days + 1, climbed + 1);
}
```

- 완전 탐색 알고리즘에서 동적 계획법으로 순서
  - 완전 탐색
  - climb(C): 지금까지 만든 날씨 조합C를 완성해서 원소의 합이 n이상이 되도록 하는 방법의 수
  - 점화식: ```climb(C) = climb(C + [1]) + climb(C + [2])```
    - C + [x]는 배열 C의 맨 뒤에 x를 덧붙인 결과이다.
    - 이 점화식은 C의 종류가 너무 많기 때문에 메모이제이션을 활용할 수 없다!
    - 날씨 조합C => C의 길이와 C의 원소의 합만을 활용한다.
  - 동적 계획법
  - climbed(days, climbed): 지금까지 만든 날씨 조합C의 크기가 days, 그 원소들의 합이 climbed일 때, C를 완성해서 원소의 합이 n이상이 되게 하는
  방법의 수
    - 최대 부분 문제의 수: ```n * m```
    - 얻은 결과의 경우의 수를 ```2^m```으로 나누면 최종 확률을 얻을 수 있다.

**SNAIL 문제**
- 위와 문제의 구조는 동일하지만, 비가 올 확률이 50%에서 75%으로 증가하였다.
- 점화식: ```climb(days, climbed) = 0.25 * climb(days + 1, climbed + 1) + 0.75 * climb(days + 1, climbed + 2)```
  - 위의 예제와 달리 날씨의 조합마다 출현할 확률이 달라지므로, 재귀함수 결과를 경우의 수가 아닌 직접 확률을 계산해야 한다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
using namespace std;
#define N 1001
double cache[N][2 * N + 1];
int n, m;

double climb(int days, int climbed)
{
	//기저 사례
	if (days == m) return climbed >= n ? 1. : 0.;
	//메모이제이션
	double &ret = cache[days][climbed];
	if (ret != -1.) return ret;
	return ret = 0.75 * climb(days + 1, climbed + 2) + 0.25 * climb(days + 1, climbed + 1);
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		for (int i = 0; i < N; i++)
			for (int j = 0; j < 2 * N + 1; j++)
				cache[i][j] = -1.;
		scanf("%d %d", &n, &m);
		printf("%.10lf\n", climb(0, 0));
	}

	return 0;
}
```
