---
title: "[ALGOSPOT]FENCE"
search: true
categories:
  - PS
tags:
  - Algospot
---

# FENCE

## 문제 링크
- [https://algospot.com/judge/problem/read/FENCE](https://algospot.com/judge/problem/read/FENCE)

## 코드 분석
- 출처: 알고리즘 문제해결전략, 분할정복
- n개의 판자를 절반으로 나눠 분할한다.
  - 부분문제 1: 가장 큰 직사각형을 왼쪽 부분 문제에서만 잘라낼 수 있다.
  - 부분문제 2: 가장 큰 직사각형을 오른쪽 부분 문제에서만 잘라낼 수 있다.
  - 부분문제 3: 가장 큰 직사각형은 왼쪽 부분 문제와 오른쪽 부분 문제에 결쳐 있다.
  - 중요 조건: 가장 큰 직사각형은 반드시 부분 문제 경계에 있는 두 판자를 포함한다.

## 결과 코드
```
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <vector>
using namespace std;
#define max(a, b) ((a) > (b) ? (a) : (b))
#define min(a, b) ((a) < (b) ? (a) : (b))

//각 판자의 높이를 저장하는 배열
vector<int> h;

//h[left,...,right]구간에서 찾아낼 수 있는 가장 큰 사각형의 넓이를 반환
int solve(int left, int right)
{
	//기저 사례: 판자가 하나밖에 없는 경우
	if (left == right) return h[left];
	//[left, mid],[mid+1, right]의 두 구간으로 문제를 분할
	int mid = (left + right) / 2;
	//분할한 문제를 각개격파(부분문제1, 부분문제2)
	int ret = max(solve(left, mid), solve(mid + 1, right));
	//부분문제3: 두 부분에 모두 걸치는 사각형 중 가장 큰 것을 찾는다.
	int lo = mid;
	int hi = mid + 1;
	int height = min(h[lo], h[hi]);
	//[mid, mid+1]만 포합하는 너비 2인 사각형을 고려한다.
	ret = max(ret, height * 2);
	//사각형이 입력 전체를 덮을 때까지 확장해 나간다.
	while (left < lo || hi < right) {
		//left ~ right 범위안에 있을 경우
		if (hi < right && (lo == left || h[lo - 1] < h[hi + 1])) {
			hi++;
			height = min(height, h[hi]);
		}
		else {
			lo--;
			height = min(height, h[lo]);
		}
		//확장한 후 사각형의 넓이
		ret = max(ret, height * (hi - lo + 1));
	}
	return ret;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		int n;
		scanf("%d", &n);
		for (int i = 0; i < n; i++) {
			int height;
			scanf("%d", &height);
			h.push_back(height);
		}
		printf("%d\n", solve(0, n - 1));
		h.clear();
	}

	return 0;
}
```

[HOME](https://codemcd.github.io/)
