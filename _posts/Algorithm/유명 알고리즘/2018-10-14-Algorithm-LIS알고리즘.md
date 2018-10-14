---
title: "[Algorithm]LCS 알고리즘"
excerpt: "수열 알고리즘"
search: true
categories:
  - Algorithm
tags:
  - 유명 알고리즘
toc: true
---

# Longest Increasing Subsequence(LIS) 알고리즘

## 개요
- 가장 긴 증가하는 부분 수열
- 예제 그림

![lis](https://user-images.githubusercontent.com/34755287/46916274-410a5980-cff3-11e8-948a-31488df44de1.JPG)

- 두번째 줄의 노란색 박스 부분이 LIS를 나타내는 모습이다.

## 구현
### 1. 동적 계획법
- [알고스팟 LIS 문제 분석](https://github.com/CODEMCD/Algorithm/blob/master/Code%20Review/ALGOSPOT/LIS.md)
  - Top-down, Bottom-up 방식
- 시간복잡도: ```O(N^2)```

### 2. Lower-bound
- 알고리즘 과정
  - num[]: 입력 수열, lis[]: LIS를 만들기 위해 비어있는 수열
  1. lis배열에 아무 값이 없다면(초기화), num배열의 가장 첫번째 값을 삽입한다.
  2. lis배열에서 가장 큰 값(= 가장 오른쪽에 있는 값)보다 현재 보고 있는 num[i]값이 크다면 lis배열에 num[i]값을 추가한다.
  3. 작다면 lower_bound(주어진 집합의 어떤 원소보다 작거나 같은 원소)를 이용하여 해당 위치에 num[i]값을 삽입한다.
- 시간복잡도: ```O(NlogN)```

___구현 코드___

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;
#define N 1001
int num[N], lis[N];

int _lower_bound(int start, int end, int target)
{
	int mid;
	while (start < end) {
		mid = (start + end) / 2;
		if (lis[mid] < target)
			start = mid + 1;
		else
			end = mid;
	}
	return end;
}

int main()
{
	int n; scanf("%d", &n);
	for (int i = 0; i < n; i++)
		scanf("%d", &num[i]);

	int idx = 0;
	lis[idx] = num[0];

	for (int i = 1; i < n; i++) {
		if (lis[idx] < num[i])
			lis[++idx] = num[i];
		else {
			int next = _lower_bound(0, idx, num[i]);
			lis[next] = num[i];
		}
	}
	printf("%d\n", idx + 1);

	return 0;
}
```

## 관련 문제
- https://www.acmicpc.net/problem/11053
- https://algospot.com/judge/problem/read/LIS
- 이외에 LIS알고리즘을 응용하는 다양한 문제들이 있다.
