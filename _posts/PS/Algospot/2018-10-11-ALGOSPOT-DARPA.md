---
title: "[ALGOSPOT]DARPA"
excerpt: "DP, 이진 탐색"
search: true
sidebar_main: true
categories:
  - PS
tags:
  - Algospot
---

# DARPA Grand Challenge

## 문제 링크
- [https://algospot.com/judge/problem/read/DARPA](https://algospot.com/judge/problem/read/DARPA)

## 코드 분석
### 최적화 문제 결정 문제로 바꿔 풀기
- 최적화 문제
  - ```optimize(locations, cameras)``` = 카메라를 설치할 수 있는 위치 locations와 카메라의 수 cameras가 주어질 때, 카메라 간 최소 간격의 최대치를
  반환한다.
- 결정 문제
  - ```decision(locations, cameras, gap)``` = 카메라를 설치할 수 있는 위치 locations와 카메라의 수 cameras가 주어질 때, 이들을 적절히 배치해
  모든 카메라의 간격이 gap이상이 되도록 하는 방법이 있는가?
  - "카메라 간의 최소 간격이 gap인 방법이 있는가?"가 아니라 "카메라들의 간의 최소 간격이 gap이상인 방법이 있는가?" 임을 유의하자.
    - 이와 같이 질문해야 최적화 문제를 풀 수 있다.
    - "이상"이라 함은 그 범위의 값들을 모두 포함할 수 있기 때문에 답의 후보 범위를 계속 반으로 줄여서 최적화를 할 수 있다.(이분법)
  - 카메라들이 항상 xkm 이상 떨어져 있다고 할때, 0km위치에 카메라를 항상 설치할 수 있다.
    - 만약, 0km에 설치하지 않는다고 하면 이를 0km로 옮겨도 똑같은 답이 된다.
    - 결과적으로, 탐욕법으로 이 문제를 해결할 수 있다는 사실을 떠올릴 수 있어야 한다.
- 구현 함수
  - ```optimize()```: 이분법을 구현하는 함수이다.
  - ```decision()```: 첫 번째로 만나는 위치에 무조건 카메라를 설치한 뒤, 각 위치를 순회하며 카메라를 설치할 수 있을때마다 카메라를 설치한다. 그 후
  설치한 카메라의 수가 문제에서 요구한 설치 개수 이상이 되면 참을 반환한다

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <vector>
using namespace std;

//결정 문제: 정렬되어 있는 locations 중 cameras를 선택해 모든 카메라 간의 간격이
//gap 이상이 되는 방법이 있는지를 반환한다.
bool decision(const vector<double> &location, int cameras, double gap) {
	//카메라를 설치할 수 있을 때마다 설치하는 탐욕적 알고리즘
	double limit = -1;
	int installed = 0;
	for (int i = 0; i < (int)location.size(); i++) {
		if (limit <= location[i]) {
			++installed;
			//location[i] + gap 이후는 되어야 카메라를 설치할 수 있다.
			limit = location[i] + gap;
		}
	}
	//결과적으로 cameras대 이상을 설치할 수 있으면 참 반환
	return installed >= cameras;
}

//최적화 문제: 정렬되어 있는 locations 중 cameras를 선택해 최소 간격을 최대화 한다.
double optimize(const vector<double> &location, int cameras) {
	double lo = 0., hi = 241.;
	//반복문 불변식: decision(lo) && !decision(hi)
	for (int it = 0; it < 100; it++) {
		double mid = (lo + hi) / 2.0;
		//간격이 mid 이상이 되도록 할 수 있으면 답은 [mid, hi]에 있다.
		if (decision(location, cameras, mid))
			lo = mid;
		//간격이 mid 이상이 되도록 할 수 없으면 답은 [lo, mid]에 있다.
		else
			hi = mid;
	}
	return lo;
}

int main()
{
	int tc; scanf("%d", &tc);
	while (tc--) {
		int n, m; scanf("%d %d", &n, &m);
		vector<double> lv;
		for (int i = 0; i < m; i++) {
			double in; scanf("%lf", &in);
			lv.push_back(in);
		}
		printf("%.2lf\n", optimize(lv, n));
	}

	return 0;
}
```
