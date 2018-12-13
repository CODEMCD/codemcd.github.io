---
title: "[Algorithm]깊이 우선 탐색(DFS) 사이클"
excerpt: "깊이 우선 탐색(DFS) 사이클"
search: true
categories:
  - Algorithm
tags:
  - Algorithm_DataStructure
sidebar_main: true
---

# 깊이 우선 탐색(DFS)로 사이클 존재 여부 확인하기

## 개요
- 미리 알아야 할 내용: DFS와 간선의 분류 링크 해주기
- 간선의 분류를 이용하면 방향 그래프에서 사이클이 존재하는지 쉽게 알 수 있다.
  - 사이클의 존재 여부는 역방향 간선의 존재 여부와 동치이다.
- 탐색 과정
  - 어떤 그래프에서 깊이 우선 탐색 중 만나는 한 정점을 u라고 하자.
  - dfs(u)는 u에서 갈 수 있는 모든 정점들을 방문한 후에 종료된다.
  - 따라서, u이전에 있는 한 정점이 dfs(u)가 종료되기 전에 u정점을 방문을 한다면, 해당 정점은 u로 가는 역방향 간선이 된다.
  - 결과적으로, 이 그래프는 사이클이 존재하는 그래프가 된다.

## 간선 구분하기
- 깊이 우선 탐색을 하면서 간선의 종류를 구분하기 위해서는 기존의 저장해야하라 정보보다 더 많은 정보가 필요하다.

**1. 해당 정점이 몇 번째로 방문 되었는지에 대한 정보(방문 순서 정보)**
- 기존의 깊이 우선 탐색에서 필요한 정보였던 해당 정점이 방문되었는지에 대한 정보를 대체한다.
- 이 정보로 (u, v)간선이 있을 때, 두 정점의 관계가 선조인지, 자손인지, 아무관계가 없는지를 알 수 있다.
- (u, v)간선이 순방향 간선인지 알 수 있다.(역방향 간선과 교차 간선은 구분하지 못함)
  - (u, v)가 순방향 간선이면 v는 u의 자손이므로, v는 u보다 더 늦게 발견된다.
  - (u, v)가 역방향 간선이면 v는 u의 선조이므로, v는 u보다 더 일찍 발견된다.
  - (u, v)가 교차 간선이면 dfs(v)가 종료된 후에 dfs(u)가 호출되므로, v는 u보다 더 일찍 발견된다.
- ```discovered[]```배열

**2. 해당 정점의 dfs()함수가 종료되었는지에 대한 정보**
- 이 정보로 (u v)간선이 역방향 간선인지 교차 간선인지 구분할 수 있다.
- (u, v)에서 dfs(v)가 아직 종료되지 않았다면 v는 u의 선조이니 역방향 간선이 되고, 종료되었다면 교차 간선이 된다.
- ```finished[]```배열

## 구현

```cpp
//그래프으이 인접 리스트 표현
vector<vector<int>> adj;
//discovered[i] = i번 정점의 발견 순서, -1로 초기화
//finished[i] = dfs(i)가 종료했으면 1, 아니면 0
vector<int> discovered, finished;
//지금까지 발견한 정점의 수
int counter;

void classifyEdgeDfs(int here) {
  discovered[here] = counter++;
  //모든 인접 정점을 순회하면서
  for(int i =0; i < adj[here].size(); ++i) {
    int there = adj[here][i];
    cout << "(" << here << "," << there << ") is a ";
    //아직 방문한 적이 없다면 방문한다. (트리 간선)
    if (discovered[there] == -1) {
      cout << "tree edge" << endl;
      classifyEdgeDfs(there);
    }
    //만약 there가 here보다 늦게 발견됐으면 there은 here의 후손이다. (순방향 간선)
    else if (discovered[here] < discovered[there])
      cout << "forward edge" << endl;
    //만약 classifyEdgeDfs(there)가 아직 종료되지 않았으면 there은 here의 선조이다. (역방향 간선 = 사이클 존재)
    else if (finished[there] == 0)
      cout << "back edge" << endl;
    //이 외의 모든 경우는 교차 간선
    else
      cout << "cross edge" << endl;
  }
  finished[here] = 1;
}
```

## 참고 문헌
- 프로그래밍 대회에서 배우는 알고리즘 문제해결전략, 인사이트, 구종만 지음
