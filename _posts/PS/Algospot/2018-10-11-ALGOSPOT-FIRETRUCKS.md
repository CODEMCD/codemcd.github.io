---
title: "[ALGOSPOT]FIRETRUCKS"
excerpt: "다익스트라 알고리즘"
search: true
sidebar_main: true
categories:
  - PS
tags:
  - Algospot
---

# 소방차

## 문제 링크
- [https://algospot.com/judge/problem/read/FIRETRUCKS](https://algospot.com/judge/problem/read/FIRETRUCKS)

## 코드 분석
### 다익스트라 알고리즘 응용
- 이 문제에서 최대 정점 개수는 1000개, 최대 간선 개수는 50만개이므로, 아래와 같은 단순한 알고리즘으로는 풀 수 없다.
  - 각 불난 위치에서 다익스트라 알고리즘을 실행하여 가장 가까운 소방서를 찾는다. (```O(nElogV)```)
  - 각 소방서마다 다익스트라 알고리즘을 실행하여 가장 가까운 불난 위치를 찾는다. (```O(mElogV)```)
  - 플로이드 알고리즘 (```O(V^3)```)
- 이 문제는 어느 소방서에서 소방차가 오느냐는 중요하지 않고, 무조건 소방서로부터 거리만 최소로 하면 된다.
- 따라서, 모든 소방서를 임의의 한 정점으로 연결한 후, 거리를 0으로 두면 단 한번의 다익스트라 알고리즘으로 문제를 해결할 수 있다.

![firetrucks](https://user-images.githubusercontent.com/34755287/46851226-c6461080-ce31-11e8-833d-103c4d08fc81.jpg)

- 위 그래프는 임의의 정점을 추가한 모습이다.
- 시간 복잡도는 다익스트라 알고리즘과 같다.

## 구현 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <queue>
#include <stack>
#include <deque>
#include <map>
using namespace std;
#define MAX_V 1001
const int INF = 987654321;

int v, e, n, m;
vector<pair<int, int>> adj[MAX_V];
//minDist: 한 소방서에서 각 불난 위치의 정점까지 최소 거리
//fireStation: 소방서 정점
//fire: 불난 위치 정점
int minDist[MAX_V], fireStation[MAX_V], fire[MAX_V];

void dijkstra(int src) {
   vector<int> dist(v + 1, INF);
   dist[src] = 0;
   priority_queue<pair<int, int>> pq;
   pq.push(make_pair(0, src));
   while (!pq.empty()) {
      int here = pq.top().second;
      int cost = -pq.top().first;
      pq.pop();
      if (dist[here] < cost) continue;
      for (int i = 0; i < adj[here].size(); ++i) {
         int there = adj[here][i].first;
         int nextDist = cost + adj[here][i].second;
         if (dist[there] > nextDist) {
            dist[there] = nextDist;
            pq.push(make_pair(-nextDist, there));
         }
         //해당 정점이 불난 위치이며, 현재까지의 거리보다 짧다면 갱신한다.
         for (int j = 0; j < n; ++j)
            if (there == fire[j] && minDist[fire[j]] > nextDist)
               minDist[fire[j]] = nextDist;
      }
   }
}

int main()
{
   int tc; scanf("%d", &tc);
   while (tc--) {
      for (int i = 0; i < MAX_V; ++i) {
         adj[i].clear();
         minDist[i] = INF;
         fireStation[i] = 0;
         fire[i] = 0;
      }

      scanf("%d %d %d %d", &v, &e, &n, &m);

      for (int i = 0; i < e; ++i) {
         int a, b, t;
         scanf("%d %d %d", &a, &b, &t);
         adj[a].push_back(make_pair(b, t));
         adj[b].push_back(make_pair(a, t));
      }
      for (int i = 0; i < n; ++i)
         scanf("%d", &fire[i]);
      //소방서의 정점을 입력 받은 후, 임의의 정점 0을 연결한다.(거리 0)
      for (int i = 0; i < m; ++i) {
         scanf("%d", &fireStation[i]);
         adj[0].push_back(make_pair(fireStation[i], 0));
      }

      //임의의 정점 0을 시작으로 다익스트라 알고리즘을 한번 실행한다.
      dijkstra(0);

      int ans = 0;
      for (int i = 0; i < n; ++i)
         ans += minDist[fire[i]];
      printf("%d\n", ans);
   }

   return 0;
}
```
