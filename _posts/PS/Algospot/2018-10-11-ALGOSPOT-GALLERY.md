---
title: "[ALGOSPOT]GALLERY"
excerpt: "그래프, 트리"
search: true
sidebar_main: true
categories:
  - PS
tags:
  - Algospot
---

# 감시 카메라 설치

## 문제 링크
- [https://algospot.com/judge/problem/read/GALLERY](https://algospot.com/judge/problem/read/GALLERY)

## 코드 분석
### 루트 없는 트리의 지배 집합 찾기
- 지배 집합(domination set): 각 정점이 자기 자신과 모든 인접한 정점들을 지배한다고 할때, 그래프의 모든 정점을 지배하는 정점들의 부분집합을 그래프의 지배 집합이라고 한다.
  - 그래프에서 지배 집합을 찾는 문제는 NP-완전 문제 중 하나로, 정점 개수의 지수 함수에 비례한다.

### 1. 루트 없는 트리(unrooted tree)
- 이 문제가 그래프가 아닌 루트 없는 트리라는 것을 알 수 있는 점은 문제 속에 있다.
  - 문제 설명의 마지막에 미술관에서 한 번 지나간 갤러리를 다시 지나기 위해서는 전에 지난 복도를 반드시 지나야 한다.
  - 사이클이 없다. = 트리로 표현할 수 있다.
- 위와 같이 그래프가 트리의 형태를 하는 것을 루트 없는 트리라고 부른다.
- 어떤 그래프가 루트 없는 트리인지를 알기 위해서는 아래와 같은 특징 중 한 가지가 성립해야 한다.(서로 동치 관계에 있음)
  - 정확히 V - 1개의 간선이 있다.
  - 사이클이 존재하지 않는다.
  - 두 정점 사이를 연결하는 단순 경로가 정확히 하나만 존재한다.

### 2. 루트 없는 트리 문제 풀기
- 루트 없는 트리를 다루는 가장 간단한 방법은 임의의 시작점에서 깊이 우선 탐색을 수행하는 것이다.
- 이 결과로 얻은 깊이 탐색 스패닝 트리는 원래 그래프의 구조를 그대로 가지면서, 부모 자식 관계르르 갖는 일반적인 트리 형태가 된다.
- 문제 설명에서 모든 갤러리가 서로 연결되어 있을 필요가 없다고 명시되어 있기 때문에, 그래프의 각 컴포넌트는 트리 형태인 것을 알 수 있다.

### 3. 트리의 지배 집합 찾기
- 트리의 최소 지배 집합을 찾는 가장 간단한 방법은 트리의 맨 아래에서부터 시작해서 위로 올라오는 것이다.
  - 잎 노드는 부모 노드와 자기 자신밖에 지배하지 못하므로, 최소 지배 집합을 만들 수 없기 때문에 절대 선택해선 안된다.
- 알고리즘 과정
  - 잎 노드는 선택하지 않는다.
  - 이 외의 노드에 대해, 트리의 맨 밑에서부터 올라오면서 다음과 같이 선택 여부를 결정 한다.
  1. 자기 자손 중 아직 지배당하지 않은 노드가 하나라도 있다면 현재 노드를 선택한다.
  2. 이 외의 경우는 현재 노드를 선택하지 않는다.
- 첫 번째 예제 데이터 그래프 형태

![gallery](https://user-images.githubusercontent.com/34755287/46851252-de1d9480-ce31-11e8-9470-34550ebe1004.JPG)


## 구현
- ```dfs(here)```는 here를 루트로 하는 서브트리를 방문하고, 반환하면서 해당 노드가 지배 집합의 일부인지, 다른 노드에 지배당하고 있는지, 지배당하지 않고 있는지 3가지 중 하나를 반환한다.
- 시간 복잡도: ```O(g + h)```

___구현 코드___

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <vector>
#include <algorithm>
#include <queue>
using namespace std;
#define MAX_V 1001
int V;
vector<int> adj[MAX_V];
vector<bool> visited;
const int UNWATCHED = 0;
const int WATCHED = 1;
const int INSTALLED = 2;
//지금까지 설치한 카메라의 총 수
int installed;
//here로부터 깊이 우선 탐색을 하고, here의 정보를 반환한다.
int dfs(int here) {
	visited[here] = true;
	int children[3] = { 0,0,0 }; //UNWATCHED, WATCHED, INSTALLED 정보
	for (int i = 0; i < adj[here].size(); ++i) {
		int there = adj[here][i];
		if (!visited[there])
			++children[dfs(there)];
	}
	//자손 노드 중 감시되지 않는 노드가 있을 경우 카메라를 설치한다.
	if (children[UNWATCHED]) {
		++installed;
		return INSTALLED;
	}
	//자손 노드 중 카메라가 설치된 노드가 있을 경우 설치할 필요가 없다.
	if (children[INSTALLED])
		return WATCHED;
	return UNWATCHED;
}

//그래프를 감시하는데 필요한 카메라의 최소 수를 반환한다.
int installCamera() {
	installed = 0;
	visited = vector<bool>(V, false);
	for (int u = 0; u < V; ++u)
		if (!visited[u] && dfs(u) == UNWATCHED)
			++installed;
	return installed;
}

int main()
{
	int tc; scanf("%d", &tc);
	while (tc--) {
		for (int i = 0; i < MAX_V; ++i)
			adj[i].clear();

		int h; scanf("%d %d", &V, &h);
		for (int i = 0; i < h; ++i) {
			int s, e; scanf("%d %d", &s, &e);
			adj[s].push_back(e);
			adj[e].push_back(s);
		}
		printf("%d\n", installCamera());
	}

	return 0;
}
```
