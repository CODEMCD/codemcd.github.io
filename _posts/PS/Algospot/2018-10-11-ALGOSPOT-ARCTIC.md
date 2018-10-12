---
title: "[ALGOSPOT]ARCTIC"
search: true
categories:
  - PS
tags:
  - Algospot
---

# 남극 기지

## 문제 링크
- [https://algospot.com/judge/problem/read/ARCTIC](https://algospot.com/judge/problem/read/ARCTIC)

## 코드 분석
### 1. 최적화 문제 결정 문제로 바꿔 풀기
- 최적화 문제: ```optimize(P)```
  - P에 주어진 기지들을 모두 연결하는 연락망을 구축할 때 가능한 최소 무전기 반경은 얼마인가?
  - 기지들을 모두 연결하는 연락망의 경우의 수는 매우 많으며, 이 중 무전기의 반경 d가 최소화되는 답을 찾고 있으므로 이 문제는 최적화 문제이다.
- 결정 문제: ```decision(P, d)```
  - 모든 기지를 하나로 연결하되, 가장 먼 두 가지 간의 거리가 d 이하인 연락망이 있는가?
  - 이 질문은 "x 또는 그보다 좋은 답이 있는가?"의 형태를 유지하고 있다.
  - 이 질문의 구현은 서로 거리가 d 이하인 기지들이 하나로 연결될 수 있는지를 확인한다.
    - 너비 우선 탐색 알고리즘
    - 두 기지 사이의 거리를 이용하는 암시적 그래프를 탐색한다.
- 시간 복잡도
  - 너비 우선 탐색의 시간 복잡도: ```O(V + E)```
  - 거리 계산: ```O(n^2)```
  - 결과적으로, ```O(n^2)```이다.

### 2. 다른 해법
- 크루스칼의 최소 스패닝 알고리즘
- 플로이드의 모든 쌍 최단 거리 알고리즘

## 결과 코드
- 최적화 문제 결정문제로 바꿔 풀기

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <vector>
#include <algorithm>
#include <cmath>
#include <queue>
using namespace std;
#define abs(a) ((a) > 0 ? (a) : (-1 * a))
#define N 101
vector<pair<double, double>> bsv;
double dist[N][N];
int n;

//두 기지 사이 거리 계산
double calcDist(int b1, int b2) {
	double dy = (bsv[b1].first - bsv[b2].first) *
		(bsv[b1].first - bsv[b2].first);
	double dx = (bsv[b1].second - bsv[b2].second) *
		(bsv[b1].second - bsv[b2].second);
	return sqrt(dy + dx);
}

//거리 d인하인 기지들만을 연결했을 때 모든 기지가 연결되는지 여부를 반환한다.
bool decision(double d) {
	vector<bool> visited(n, false);
	visited[0] = true;
	queue<int> q;
	q.push(0);
	int seen = 0;
	while (!q.empty()) {
		int here = q.front();
		q.pop();
		++seen;
		for(int there = 0; there < n; ++there)
			if (!visited[there] && dist[here][there] <= d) {
				visited[there] = true;
				q.push(there);
			}
	}
	return seen == n;
}

//모든 기지를 연결할 수 있는 최소 d를 반환한다.
double optimize() {
	double lo = 0, hi = 1416.00;
	for (int it = 0; it < 100; ++it) {
		double mid = (lo + hi) / 2;
		//mid가 가능하다면, 더 좋은(작은) 답을 찾는다.
		if (decision(mid))
			hi = mid;
		//mid가 불가능하다면, 더 나쁜(큰) 답을 찾는다.
		else
			lo = mid;
	}
	return hi;
}

int main()
{
	int tc; scanf("%d", &tc);
	while(tc--) {
		bsv.clear();
		memset(dist, 0, sizeof(dist));

		scanf("%d", &n);
		for (int i = 0; i < n; i++) {
			double y, x; scanf("%lf %lf", &y, &x);
			bsv.push_back({ y, x });
		}
		for (int i = 0; i < n; i++)
			for (int j = 0; j < n; j++)
				dist[i][j] = calcDist(i, j);
		printf("%.2lf\n", optimize());
	}

	return 0;
}
```
