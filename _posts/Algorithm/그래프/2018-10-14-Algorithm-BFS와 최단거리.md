---
title: "[Algorithm]너비 우선 탐색(BFS)"
excerpt: "BFS를 활용하여 최단 거리 구하기"
search: true
categories:
  - Algorithm
tags:
  - 그래프
toc: true
---

# 너비 우선 탐색(BFS)과 최단 거리 알고리즘

## 너비 우선 탐색으로 최단 거리 구하기
- 가중치가 없는 그래프에 대해 주로 사용할 수 있다.
- 최단 경로를 구성하는 정점들의 목록을 구할 수 있다.

### 넓이 우선 탐색으로 최단거리 구하는 과정
- 간선(u, v)를 통해 정점 v를 처음 발견하여 큐에 넣었다.
- 시작점으로부터 정점 v까지 최단 거리 ```distance[v]```는 시작적으로부터 정점 u까지 최단 거리 ```distance[u]```에 1을 더한 것이다.
- 너비 우선 탐색 스패닝 트리를 보면 시작점으로부터 각 정점이 모두 실제 그래프상 최단 경로임을 알 수 있다.

![bfsst](https://user-images.githubusercontent.com/34755287/46914776-04cbfe80-cfdd-11e8-9d8b-702d8d674639.JPG)

### 구현
- ```bfs()```: 각 정점까지의 최단 거리를 계산하고, 너비 우선 탐색 스패닝 트리를 만든다.
- ```shortestPath()```: 너비 우선 탐색 스패닝 트리와 정점을 입력받아, 루트로부터 해당 입력된 정점까지의 최단 경로를 반환한다.
  - 루트로부터 입력 정점까지 경로와 입력 정점으로부터 루트까지 경로가 같은 것을 이용한다.
  - BFS 스패닝 트리에서 각 정점의 부모 정점을 저장한 배열을 이용하여 계산한다.(```parent[]```)

```cpp
vector<vector<int>> adj;
//start에서 시작해 그래프를 너비 우선 탐색하고 시작점부터 각 정점까지의
//최단 거리와 너비 우선 탐색 스패닝 트리를 계산한다.
//distance[i] = start부터 i까지의 최단 거리
//parent[i] = 너비 우선 탐색 스패닝 트리에서 i의 부모 번호(루트인 경우 자신의 번호)
void bfs(int start, vector<int> &distance, vector<int> &parent) {
	distance = vector<int>(adj.size(), -1);
	parent = vector<int>(adj.size(), -1);
	//방문할 정점 목록을 유지하는 큐
	queue<int> q;
	distance[start] = 0;
	parent[start] = start;
	q.push(start);
	while (!q.empty()) {
		int here = q.front();
		q.pop();
		//here의 모든 인접한 정점을 검사한다.
		for (int i = 0; i < adj[here].size(); ++i) {
			int there = adj[here][i];
			//처음 보는 정점이면 방문 목록에 집어넣는다.
			if(distance[there] == -1) {
				q.push(there);
				distance[there] = distance[here] + 1;
				parent[there] = here;
			}
		}
	}
}

//v로부터 시작점까지의 최단 경로를 계산한다.
vector<int> shortestPath(int v, const vector<int> &parent) {
	vector<int> path(1, v);
	while (parent[v] != v) {
		v = parent[v];
		path.push_back(v);
	}
	reverse(path.begin(), path.end());
	return path;
}
```

## 상태 객체와 너비 우선 탐색을 사용하여 최단 경로 구하기
### 예제: 15-퍼즐
- 4 X 4 격자에 끼워진 15개의 임의의 순서의 숫자 타일이 주어졌을 때, 순서에 맞게 맞추는 퍼즐 문제이다.
- 게임판의 상태를 정점으로 표현하여 그래프를 만든 후, 문제를 해결할 수 있다.
  - 상태를 정점으로 하는 그래프를 상태 공간(state space)라고 부른다.
- 게임판에 총 15개의 숫자와 1개의 빈칸이 있으므로, 상태 공간은 총 ```16!```개의 정점을 갖는다.
- 한 번의 움직임으로 한 상태를 다른 상태로 바꿀 수 있을 때, 두 정점을 간선으로 연결한다.
  - 각 정점마다 최대 4개의 이웃 정점이 있을 수 있다.(위, 아래, 오른쪽, 왼쪽으로 옮길 수 있기 때문)
### 15-퍼즐 구현
- 게임판의 상태를 ```State```라는 별도의 객체로 표현한다.
  - 15-퍼즐에 관련된 부분을 전부 ```State``` 객체 내에 구현함으로서 ```bfs()```함수와 독립적이다.
  - ```State```만 바꾸면 다른 문제에서도 활용할 수 있다.
- ```State```가 가질 수 있는 값의 종류가 너무 많기 때문에 배열 대신 map을 사용한다.
- 아직 덜구현된듯 문제에 맞게 구현해서 예제 넣어 풀어보기(State 클래스 구현)

```cpp
//게임판의 상태를 표현한다.
class State {
	//인접한 상태들의 목록을 반환한다.
	vector<State> getAdjacent() const;
	//map에 State를 넣기 위한 비교 연산자
	bool operator < (const State &rhs) const;
	//종료 상태와 비교하기 위한 연산자
	bool operator == (const State &rhs) const;
};
typedef map<State, int> stateMap;

//start에서 finish까지 가는 최단 경로의 길이를 반환한다.
int bfs(State start, State finish) {
	//예외: start == finish인 경우
	if (start == finish) return 0;
	//각 정점까지의 최단 경로의 길이를 저장한다.
	stateMap c;
	//앞으로 방문할 정점들을 저장한다.
	queue<State> q;
	q.push(start);
	c[start] = 0;
	//너비 우선 탐색
	while (!q.empty()) {
		State here = q.front();
		q.pop();
		int cost = c[here];
		//인접한 정점들의 번호를 얻어낸다.
		vector<State> adjacent = here.getAdjacent();
		for (int i = 0; i < adjacent.size(); ++i) {
			if (c.count(adjacent[i]) == 0) {
				//답을 찾았나?
				if (adjacent[i] == finish) return cost + 1;
				c[adjacent[i]] = cost + 1;
				q.push(adjacent[i]);
			}
		}
	}
	//답을 찾지 못한 경우
	return -1;
}
```

### 시간 복잡도 분석
- 이 문제는 답을 찾는 대로 탐색을 종료하기 때문에, 일반적인 너비 우선 탐색의 시간복잡도 ```O(|V| + |E|)```와 다르다.
- 너비 우선 탐색이 방문하는 정점의 개수에 비례한다.
- 이 문제에서 너비 우선 탐색이 방문하는 정점의 개수에 직접적인 영향을 주는 요소는 시작 정점에서 목표 정점까지의 최단 거리 d와 탐색의 분기 수(branching factor) b이다.
  - 분기 수는 경로의 길이가 하나 늘어날 때마다 닿을 수 있는 정점의 개수를 말한다.
  - 이 문제에서 분기 수는 최대 4개 이지만, 이 중에는 돌아가는 정점이나 중복된 정점이 있기 때문에 2나 3으로 계산할 수 있다.
- 이를 바탕으로, 시간 복잡도는 대략 ```O(b^d)```가 된다.

## 너비 우선 탐색보다 더 효율적인 최단 경로 알고리즘
- 양방향 탐색
- 점점 깊어지는 탐색(Iteratively Deeping Search, IDS)

## 탐색 방법 선택하기
1. 상태 공간에서의 최단 경로를 찾는 경우, 너비 우선 탐색을 최우선적으로 고려한다.
  - 탐색의 깊이 한계가 정해져 있지 않거나 너무 깊어서 메모리 사용량이 너무 크지 않은지 확인해야 한다.
2. 탐색의 최대 깊이가 정해져 있고 너비 우선 탐색을 하기에는 메모리와 시간이 부족한 경우 양방향 탐색을 고려한다.
3. 위의 두 탐색이 모두 메모리나 시간이 초과될 경우, 점점 깊어지는 탐색을 사용한다.


## 참고 문헌
- 프로그래밍 대회에서 배우는 알고리즘 문제해결전략, 인사이트, 구종만 지음
