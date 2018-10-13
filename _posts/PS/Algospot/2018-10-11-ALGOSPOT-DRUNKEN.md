---
title: "[ALGOSPOT]DRUNKEN"
excerpt: "플로이드 알고리즘"
search: true
categories:
  - PS
tags:
  - Algospot
---

# 음주 운전 단속

## 문제 링크
- [https://algospot.com/judge/problem/read/DRUNKEN](https://algospot.com/judge/problem/read/DRUNKEN)

## 코드 분석
### 플로이드 최단 거리 알고리즘
- 플로이드 알고리즘은 아무 정점도 경유하지 않은 최단 거리에서 시작해 경유할 수 있는 정점으르 하나씩 추가해 가며 최단 거리를 갱신한다.
  - 이 과정에서 0번부터 V-1번까지 정점들을 순서대로 추가하지만, **이 순서는 단지 편의를 위함이지 순서가 바뀐다고 해도 알고리즘의 정당성에는 어떠한 영향도 미치지 않는다.**
- 위 속성을 이용하여, 각 정점을 단속에 걸리는 시간 별로 오름차순으로 정렬한 뒤, 순서대로 플로이드 알고리즘을 수행하면 해당 문제의 답을 찾을 수 있다.
  - 단속 시간이 짧은 정점부터 알고리즘을 수행하면, 순서대로 한 경유점의 가중치를 포함한 최단 거리를 계산하면서 가장 가중치가 큰 경유점에서의 최단 거리 경로를 찾을 수 있다. (오름차순으로 가중치가 가장 큰 경유점이 마지막에 있기 때문)
  - 이는 플로이드 알고리즘은 반복문을 사용하기 때문에 경유점의 가중치가 입력된 순서가 결과값에 영향을 미치기 때문이다.
- ```C_k(u, v)``` = k 혹은 그보다 단속 시간이 적게 걸리는 정점들만을 경유해서 u에서 v로 가는 최단 거리
- ```W[][]```: 최소 예상 시간
- ```E[w]```: 정점 w에서 소요되는 단속 시간
```
W[u][v] = min(C_w(u, w) + C_w(w, v) + E[w])
```

## 구현 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;
#define N 501
const int INF = 987654321;
int map[N][N];
//각 정점까지 음주단속 시간을 포함한 가장 짧은 시간을 저장
int W[N][N];
//각 정점에서 음주 운전 단속을 할 때 걸리는 시간
int delay[N];
int v, e;

void solve() {
	//모든 정점들을 예상 시간 별로 정렬한다.
	vector<pair<int, int>> order;
	for (int i = 0; i < v; ++i)
		order.push_back(make_pair(delay[i], i));
	sort(order.begin(), order.end());
	//정점을 하나도 거치지 않고 얻을 수 있는 최단 경로
	for (int i = 0; i < v; ++i)
		for (int j = 0; j < v; ++j)
			if (i == j)
				W[i][j] = 0;
			else
				W[i][j] = map[i][j];

	for (int k = 0; k < v; ++k) {
		//k번째로 예상 시간이 적게 걸리는 정점 w까지를 지나서 얻을 수 있는 최단 거리
		int w = order[k].second;
		for(int i=0; i<v; ++i)
			for (int j = 0; j < v; ++j) {
				map[i][j] = min(map[i][j], map[i][w] + map[w][j]);
				W[i][j] = min(W[i][j], map[i][w] + delay[w] + map[w][j]);
			}
	}
}

int main()
{
	scanf("%d %d", &v, &e);
	for (int i = 0; i < v; ++i)
		scanf("%d", &delay[i]);
	for (int i = 0; i < e; ++i) {
		int a, b, c;
		scanf("%d %d %d", &a, &b, &c);
		map[a - 1][b - 1] = c;
		map[b - 1][a - 1] = c;
	}

	for (int i = 0; i < v; ++i)
		for (int j = 0; j < v; ++j)
			if (!map[i][j])
				map[i][j] = INF;

	solve();

	int tc; scanf("%d", &tc);
	while (tc--) {
		int start, end;
		scanf("%d %d", &start, &end);
		printf("%d\n", W[start - 1][end - 1]);
	}

	return 0;
}
```
