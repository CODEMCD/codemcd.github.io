---
title: "[Algorithm]절단점 찾기 알고리즘"
excerpt: "절단점, SCC"
search: true
categories:
  - Algorithm
tags:
  - 그래프
toc: true
sidebar_main: true
---

# 깊이 우선 탐색(DFS)를 활용한 절단점 관련 알고리즘

## 절단점이란?
- 절단점(cut vertex): 어떤 무향 그래프의 절단점이란 이 점과 인접한 간선들을 모두 지웠을 때 해당 컴포넌트가 2개 이상으로 나뉘어지는 정점을 말한다.

![cutvertex1](https://user-images.githubusercontent.com/34755287/46916143-8fb6f400-cff1-11e8-8f3f-1b572c3ce534.JPG)

- 위 그래프에서 1, 3, 5 정점이 각각 절단점이다.

## 절단점 찾기 알고리즘
- 무향 그래프에서 한 번의 깊이 우선 탐색으로 그래프의 모든 절단점으르 찾을 수 있다.
- 무향 그래프에서 절단점을 포함하지 않는 서브그래프를 이중 결합 컴포넌트(biconnected component)라고 부른다.
  - 이중 결합 컴포넌트에서 임의의 한 정점을 지우더라도 정정 간의 연결 관계는 유지된다.
### 탐색 과정
- 임의의 정점에서부터 깊이 우선 탐색을 수행하여 DFS 스패닝 트리를 만든다.
- 무향 그래프에서 어떤 정점 u는 연결된 모든 정점들은 u의 선조 아니면 자손 관계에 있다.
  - 무향 그래프에서는 교차 간선이 없기 때문이다.
  - 따라서, u의 자손들을 루트로 하는 서브트리들은 서로 연결되어 있지 않다.
- 위의 조건을 이용하면 u가 지워졌을 때, 그래프가 쪼개지지 않는 유일한 경우는 u의 선조와 자손들이 서로 전부 역방향 간선으로 연결되어 있는 경우이다.

![cutvertex2](https://user-images.githubusercontent.com/34755287/46916144-8fb6f400-cff1-11e8-89f0-2d2c9027ea23.JPG)

  - u의 자손들이 전부 역방향 간선을 통해 u의 선조로 갈 수 있다면, u는 절단점이 아니다.
- u가 스패닝 트리의 루트라서 선조가 없는 경우는 둘 이상의 자손을 가질때만 절단점이 될 수 있다.
  - u의 선조가 없다고 무조건 절단점이 되는 것은 아니다.
  - u의 자손이 하나도 없거나 하나만 있을 경우, u정점이 지워져도 그래프는 쪼개지지 않는다.

## 구현
- u정점의 조상들이 항상 u보다 먼저 발견되어야 한다는 점을 활용하여, 깊이 우선 탐색이 해당 정점을 루트로 하는 서브트리에서 역방향 간선을 통해 닿는 정점들의 최소 발견순서를 반환한다.

```cpp
//그래프의 인접 리스트 표현
vector<vector<int>> adj;
//각 정점의 발견 순서, -1로 초기화
vector<int> discovered;
//각 정점이 절단점인지 여부를 저장한다. false로 초기화
vector<bool> isCutVertex;
int counter = 0;
//here를 루트로 하는 서브트리에 있는 절단점들을 찾는다.
//반환 값은 해당 서브트리에서 역방향 간선으로 갈 수 있는 정점 중 가장 일찍 발견된 정점의 발견 시점.
//처음 호출은 isRoot = true이다.
int findCutVertex(int here, bool isRoot) {
  //발견 순서를 기록한다.
  discovered[here] = counter++;
  int ret = discovered[here];
  //루트인 경우의 절단점 판정을 위해 자손 서브트리의 개수를 센다.
  int children = 0;
  for(int i = 0; i < adj[here].size(); ++i) {
    int there = adj[here][i];
    if (discovered[there] == -1) {
      ++children;
      //이 서브트리에서 갈 수 있는 가장 높은 정점의 번호
      int subtree = findCutVertex(there, false);
      //그 번호가 자기 자신 이하라면 현재 위치는 절단점!
      if (!isRoot && subtree >= discovered[here])
        isCutVertex[here] = true;
      ret = min(ret, subtree);
    }
    else
      ret = min(ret, discovered[there]);
  }
  //루트인 경우 절단점 판정은 서브트리의 개수로 한다.
  if(isRoot) isCutVertex[here] = (children >= 2);
  return ret;
}
```

## 강결합 컴포넌트 (Strongly Connected Components, SCC) 분리
- 방향 그래프에서는 무향 그래프의 절단점 정의를 그대로 사용할 수 없으므로, 이중 결합 컴포넌트를 정의할 수 없다.
- 방향 그래프에서는 이중 결합 컴포넌트와 유사한 강결합 컴포넌트가 있다.
  - 방향 그래프 상에서 두 정점 u, v에 대해 양 방향으로 가는 경로가 모두 있을 때, 두 정점은 같은 SCC에 속해 있다고 말한다.

![scc1](https://user-images.githubusercontent.com/34755287/46916145-904f8a80-cff1-11e8-8af7-cc7e9afdab1a.JPG)

- 위 그림은 한 그래프의 각 SCC들을 짙은 색 배경으로 구분한 모습이다.
- SCC 특징
  - 위 그래프에서도 볼 수 있듯이, SCC 사이를 연결하는 간선들을 모으면 각 SCC들을 정점으로 하는 DAG를 만들 수 있다.
    - 원 그래프의 정점들을 SCC별로 분리하고 각 SCC를 표현하는 정점들을 갖는 새로운 그래프를 만드는 과정을 그래프 압축(condensation)이라 부른다.
  - 한 사이클에 포함된 정점들은 항상 같은 SCC에 속한다.
  - 반대로 한 SCC에 속한 두 정점 사이를 잇는 양방향 경로를 합치면 두 정점을 포함하는 사이클이 된다.

### 강결합 컴포넌트 분리를 위한 타잔(Tarjan)의 알고리즘
- 타잔 알고리즘은 한 번의 깊이 우선 탐색으로 강결합 컴포넌트를 분리할 수 있다.

#### 1. 알고리즘 과정
- 임의의 정점에서부터 깊이 우선 탐색을 수행하여 DFS 스패닝 트리를 만든다.

![scc2](https://user-images.githubusercontent.com/34755287/46916146-904f8a80-cff1-11e8-8ced-7360a07f6278.JPG)

- 위 그림은 예제 그래프의 가장 왼쪽 정점에서 깊이 우선 탐색을 해서 얻은 스패닝 트리이다.
  - 스패닝 트리를 적절히 자르기만 하면 정점들을 SCC로 분리할 수 있다는 것을 알 수 있다.
- 동심원으로 표현된 정점들을 각 SCC의 루트라고 하자.
- 이때 SCC를 분리하기 위해 끊어야할 간선들은 모두 SCC의 루트로 내려가는 트리 간선임을 알 수 있다.
  - 스패닝 트리에서 인접한 두 정점이 서로 다른 SCC에 속한다면, 그 중 자손 정점은 자신이 속한 SCC의 루트여야만 한다.
- 한 정점이 해당 SCC의 루트에 속하는지 알기 위해서는 절단점 찾기 알고리즘을 응용하여 판단할 수 있다.
  - 어떤 정점 u가 SCC의 루트라면, u의 선조와 u가 서로 다른 SCC에 속한다는 의미이다.
  - 위의 조건을 만족하려면, u에서 그 선조로 가는 경로가 없어야하는데 이 경로를 탐색 없이 확인하는 방법은 절단점 찾기 알고리즘을 응용해야 한다.
- u를 루트로 하는 서브 트리 깊이 우선 탐색하면서 만나는 모든 역방향 간선을 이용해 닿을 수 있는 가장 상위 정점을 찾는다.
- 이 정점이 u의 선조거나 그보다 높이 있다면 이 역방향 간선을 이용해 u에서 그 선조로 갈 수 있으며, 따라서 u는 SCC의 루트가 아니다.
- 방향 그래프에서는 교차 간선이 존재하므로, 이를 유의해야 한다.
    - 어떤 정점이 SCC의 루트인데, 자손 중 하나가 교차 간선을 통해 다른 정점에 연결되어 있다면 판정이 잘못 될 수 있다.

![scc3](https://user-images.githubusercontent.com/34755287/46916147-904f8a80-cff1-11e8-8523-f40938aefea5.JPG)

    - 위 그림에서 u에서 깊이 우선 탐색을 시작하여 왼쪽의 SCC를 먼저 탐색한 뒤 v로 왔다고 한다면, w에서 왼쪽으로 가는 교차 간선을 v의 선조로 가는 역방향 간선이라고 판단하여 v가 SCC의 루트가 아니라고 잘못된 판단을 내린다.
    - 해결 방법은 그래프의 모든 교차 간선은 무시한다.

#### 2. 구현
- 현재 위치를 루트로 하는 서브트리에 남아 있는 정점들을 모두 찾는 방법을 스택을 이용하여 구현한다.
  - ```scc()```함수는 깊이 우선 탐색 과정에서 지금까지 방문한 정점 중 아직 SCC로 묶이지 않은 모든 정점들을 스택에 담는다.
  - 각 정점을 처음 방문할 때마다 스택에 넣기 때문에, here가 SCC의 루트라는 것을 확인하면 스택에서 here 위에 있는 정점들은 모두 here의 후손이다.
  - 따라서, here를 만날 때까지 계속 스택에서 정점을 꺼내는 방식으로 쉽게 구현이 가능하다.
- 시간 복잡도: ```O(|V| + |E|)```
- SCC의 위상 정렬
  - 아래의 코드에서 새 SCC가 생성되는 시점은 항상 ```scc()```함수가 종료하기 직전이다.
  - 위와 같은 속성때문에, 각 SCC는 위상 정렬의 역순으로 번호가 매겨진다. (```dfs()```가 늦게 종료하는 정점부터 배열하면 위상 정렬 결과가 나온다)
  - 그래프의 압축을 바로 사용할 수 있다.

- 구현 코드 및 예제

___구현 코드___

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstdio>
#include <vector>
#include <algorithm>
#include <stack>
using namespace std;

vector<vector<int>> adj;
//각 정점의 컴포넌트 번호, 컴포넌트 번호는 0부터 시작하며,
//같은 강결합 컴포넌트에 속한 정점들의 컴포넌트 번호가 같다.
vector<int> sccId, discovered, finished;
stack<int> st;  //정점의 번호를 담는 스택
int sccCounter, vertexCounter;
//here를 루트로 하는 서브트리에서 역방향 간선으로 닿을 수 있는 최소의 발견 순서를 반환한다.
int scc(int here) {
	int ret = discovered[here] = vertexCounter++;
	//스택에 here를 넣는다. here의 후손들은 모두 스택에서 here 후에 들어간다.
	st.push(here);
	for (int i = 0; i < adj[here].size(); ++i) {
		int there = adj[here][i];
		//(here, there)가 트리 간선
		if (discovered[there] == -1)
			ret = min(ret, scc(there));
		//(here, there)가 역행 간선인 경우 선조 정점의 발견 순서를 확인한다.
		else if (discovered[there] < discovered[here] && finished[there] == -1)
			ret = min(ret, discovered[there]);
	}
	//here가 강결합 컴포넌트의 루트인지 확인한다.
	if (ret == discovered[here]) {
		//here를 루트로 하는 서브트리에 남아 있는 정점들을 전부 하나의 컴포넌트로 묶는다.
		while (true) {
			int t = st.top();
			st.pop();
			sccId[t] = sccCounter;
			if (t == here) break;
		}
		++sccCounter;
	}
	finished[here] = 1;
	return ret;
}

//tarjan의 SCC알고리즘
vector<int> tarjanSCC() {
	//배열과 카운터 초기화
	sccId = discovered = finished = vector<int>(adj.size(), -1);
	sccCounter = vertexCounter = 0;
	//모든 정점에 대해 scc()호출
	for (int i = 0; i < adj.size(); ++i)
		if (discovered[i] == -1) scc(i);
	return sccId;
}

int main()
{
	//n: 정점의 개수, m: 간선의 개수
	int n, m; scanf("%d %d", &n, &m);
	adj = vector<vector<int>>(n);
	for (int i = 0; i < m; i++) {
		int s, e; scanf("%d %d", &s, &e);
		adj[s].push_back(e);
	}
	vector<int> ans;
	ans = tarjanSCC();
	for (int i = 0; i < n; ++i)
		printf("%d ", ans[i]);
	return 0;
}
```
- 예제 그래프(위의 예제 그래프에 정점 번호 부여)

![sccex](https://user-images.githubusercontent.com/34755287/46916157-bd9c3880-cff1-11e8-8ec4-d1f39fd04a44.JPG)

- 예제 입력
```
11 15
0 1
0 7
1 2
2 0
2 3
3 4
3 6
4 5
5 3
7 8
7 9
8 6
8 9
9 10
10 8
```
- 예제 결과
```
4 4 4 1 1 1 0 3 2 2 2
```

| 정점 번호 | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
|:---------:|:-:|:-:|:-:|---|---|---|---|---|---|---|----|
|  SCC 번호 | 4 | 4 | 4 | 1 | 1 | 1 | 0 | 3 | 2 | 2 | 2  |

## 참고 문헌
- 프로그래밍 대회에서 배우는 알고리즘 문제해결전략, 인사이트, 구종만 지음
