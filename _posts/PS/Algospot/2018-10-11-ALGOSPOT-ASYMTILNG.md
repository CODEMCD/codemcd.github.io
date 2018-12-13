---
title: "[ALGOSPOT]ASYMTILING"
excerpt: "DP"
search: true
sidebar_main: true
categories:
  - Algorithm
tags:
  - Algorithm_Algospot
---

# 비대칭 타일링

## 문제 링크
- https://algospot.com/judge/problem/read/ASYMTILING](https://algospot.com/judge/problem/read/ASYMTILING)

## 코드 분석
### 전체 타일링 경우의 수 - 대칭 타일링 경우의 수
- 전체 타일링 경우의 수는 쉽게 구할 수 있다.
- 비대칭 타일링 경우의 수를 찾는 것 보다 대칭 타일링 경우의 수를 찾는 것이 더욱 간단하다.

![asmytiling1](https://user-images.githubusercontent.com/34755287/46851055-26888280-ce31-11e8-804c-b970edc94f47.JPG)

- n이 홀수인 경우, 위 그림의 (a)에 해당한다.
- n이 짝수인 경우, 위 그림의 (b)와 (c)에 해당한다.
- 회색부분은 양쪽이 대칭이기 때문에 한 부분의 경우의 수만 빼주면 된다.
- 두 값을 빼줄 때, mod계산에 주의하자!
- 시간복잡도: 전체 타일링의 경우의 수 ```O(n)``` + 대칭 타일링 경우의 수 ```O(n)``` = 총 ```O(n)``` 이다.

### 비대칭 타일링 경우의 수
- 총 4가지 패턴의 비대칭 방법이 있다.

![asmytiling2](https://user-images.githubusercontent.com/34755287/46851056-27211900-ce31-11e8-8957-b8bd58dbe71f.JPG)

- (a), (b): 가운데 남은 회색 부분을 덮은 방법을 재귀 호출로 찾는다. 물론 이 방법은 대칭이 아니어야 한다.
- (c), (d): 가운데 남은 회색 부분을 덮는 방법을 찾으며, 이는 대칭이어도 상관없다.
- 시간복잡도: ```O(n)```
- 답이 제대로 나오지 않음 - 보류...

## 결과 코드
- 전체 타일링 경우의 수 - 대칭 타일링 경우의 수

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;
#define N 101
#define M 1000000007
int tile[N];

int asymmetric(int width)
{
	if (width % 2 == 1)  //(a)
		return (tile[width] - tile[width / 2] + M) % M;
	int ret = tile[width];
	ret = (ret - tile[width / 2 - 1] + M) % M;  //(b)
	ret = (ret - tile[width / 2] + M) % M;      //(c)
	return ret;
}

int main(void)
{
	//전체 타일링 경우의 수 구하기
	tile[1] = 1; tile[2] = 2;
	for (int i = 3; i < N; i++)
		tile[i] = (tile[i - 1] % M + tile[i - 2] % M) % M;

	int tc;
	scanf("%d", &tc);
	while (tc--) {
		int n;
		scanf("%d", &n);
		if (n == 1 || n == 2)
			printf("0\n");
		else
			printf("%d\n", asymmetric(n));
	}

	return 0;
}
```
