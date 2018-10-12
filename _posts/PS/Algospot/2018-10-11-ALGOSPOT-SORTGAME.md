---
title: "[ALGOSPOT]SORTGAME"
search: true
categories:
  - PS
tags:
  - Algospot
---

# Sorting Game

## 문제 링크
- [https://algospot.com/judge/problem/read/SORTGAME](https://algospot.com/judge/problem/read/SORTGAME)

## 코드 분석
### 그래프화한 뒤 넓이 우선 탐색(BFS) 사용하기
**1. 그래프로 바꾸기**
- 각 배열을 정점으로 표현하고, 부분 구간을 뒤집어 한 배열에서 다른 배열을 만들 수 있을 때 두 정점을 간선으로 연결한다.
- n원소가 있을 때 이들을 배열하는 방법은 ```n!```이며, 최대 정점의 개수도 이와 같다.

![sortgame](https://user-images.githubusercontent.com/34755287/46851493-a236ff00-ce32-11e8-88b5-1db02f35eb1d.JPG)

- 위 그래프를 넓이 우선 탐색하면서 정렬된 상태를 나타내는 정점까지의 거리를 계산한다.
- 구현
  - 그래프를 생성하는 과정은 생략하고, 입력 배열의 부분 구간을 뒤집으면서 그때 그때 그래프의 간선을 만들어 넓이 우선 탐색을 실행한다.
  - 정점 큐의 각 원소를 정수 배열로 하며, 거리를 계산할 ```distance[]```는 정수 배열을 키(key)로 갖는 map을 사용한다.

```cpp
//perm을 정렬하기 위해 필요한 최소 뒤집기 연산의 수를 계산 후 반환한다.
int bfs(const vector<int> &perm) {
	int n = perm.size();
	//목표 정점을 미리 계산한다.(오름차순된 배열)
	vector<int> sorted = perm;
	sort(sorted.begin(), sorted.end());
	//방문 목록(큐)
	queue<vector<int>> q;
	//시작점으로부터 각 정점까지의 거리(map)
	map<vector<int>, int> distance;
	//시작점의 거리는 0
	distance[perm] = 0;
	//시작점을 큐에 넣는다.
	q.push(perm);
	while (!q.empty()) {
		vector<int> here = q.front();
		q.pop();
		//목표 정점을 발견했으면 곧장 종료한다.
		if (here == sorted) return distance[here];
		int cost = distance[here];
		//가능한 모든 부분 구간을 뒤집어 본다.
		for (int i = 0; i < n; ++i) {
			//reverse함수의 배열 범위에 주의한다.
			for (int j = i + 1; j <= n; ++j) {
				reverse(here.begin() + i, here.begin() + j);
				if (distance.count(here) == 0) {
					distance[here] = cost + 1;
					q.push(here);
				}
				reverse(here.begin() + i, here.begin() + j);
			}
		}
	}
	//실행되지 않음
	return -1;
}
```

**2. 더 빠른 해결방법**
- 위 방법은 최악의 경우 ```8! = 40320```개의 정점을 탐색하며, map에 접근해야한다. 그리고 최대 1000개의 테스트 케이스가 있기 때문에 시간안에 해결하기 힘들다.
- 위 알고리즘을 최적화하기 위해 2가지를 알고 있어야 한다.
  - 숫자들이 다르더라도 상대적인 크기가 같은 배열들에 대한 답은 같다.
    - 예를들어, 두 배열 {30, 40, 10, 20}과 {3, 4, 1, 2}는 모두 최소 연산수가 2이다.
  - 이 문제의 그래프는 양방향 그래프이기 때문에, 시작 정점에서 목표 정점으로 가는 최단 거리는 그 반대인 목표 정점에서 시작 정점으로 가는 최단 거리와 같다.
    - 한 배열을 정렬하는데 드는 연산의 수는 정렬된 배열을 원래 배열로 바꾸는데 드는 연산의 수와 같다.
- 위 2가지 속성을 이용하면, 모든 n(최대 크기 8)에 대해 정렬된 배열 [0, 1, 2, ..., n-1]에서 모든 상태를 미리 계산한다.
- 입력 배열을 [0, n-1] 범위의 값으로 상대적인 크기를 유지한 상태로 바꾸면 위에서 미리 계산된 연산의 수로 답을 바로 알 수 있다.

**3. 구현 코드**

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <vector>
#include <algorithm>
#include <queue>
#include <map>
using namespace std;

map<vector<int>, int> toSort;
//[0, ..., n-1]의 모든 순열에 대해 toSort[]를 계산해 저장한다.
void precalc(int n) {
	vector<int> perm(n);
	for (int i = 0; i < n; ++i) perm[i] = i;
	queue<vector<int>> q;
	q.push(perm);
	toSort[perm] = 0;
	while (!q.empty()) {
		vector<int> here = q.front();
		q.pop();
		int cost = toSort[here];
		for (int i = 0; i < n; ++i) {
			for (int j = i + 1; j <= n; ++j) {
				reverse(here.begin() + i, here.begin() + j);
				if (toSort.count(here) == 0) {
					toSort[here] = cost + 1;
					q.push(here);
				}
				reverse(here.begin() + i, here.begin() + j);
			}
		}
	}
}

int solve(const vector<int> &perm) {
  //perm을 [0, ..., n-1]의 순열로 변환한다.
	int n = perm.size();
	vector<int> fixed(n);
	for (int i = 0; i < n; ++i) {
		int smaller = 0;
		for (int j = 0; j < n; ++j) {
			if (perm[j] < perm[i])
				smaller++;
		}
		fixed[i] = smaller;
	}
	return toSort[fixed];
}

int main()
{
	for (int i = 1; i <= 8; ++i)
		precalc(i);

	int tc; scanf("%d", &tc);
	while (tc--) {
		int n; scanf("%d", &n);
		vector<int> in(n);
		for (int i = 0; i < n; ++i)
			scanf("%d", &in[i]);
		printf("%d\n", solve(in));
	}

	return 0;
}
```
