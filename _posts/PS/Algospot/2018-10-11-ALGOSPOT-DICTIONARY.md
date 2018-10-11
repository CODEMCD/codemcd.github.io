---
title: "[ALGOSPOT]DICTIONARY"
search: true
categories:
  - PS
tags:
  - Algospot
---

# 고대어 사전

## 문제 링크
- [https://algospot.com/judge/problem/read/DICTIONARY](https://algospot.com/judge/problem/read/DICTIONARY)

## 코드 분석
### 위상 정렬 문제
**1. 그래프 모델링**
- 각 알파벳을 정점으로 표현하고, 한 알파벳이 다른 알파벳 앞에 와야 할 때 두 정점을 방향 간선으로 연결한다.
  - 문제가 원하는 알파벳들의 순서는 위 그래프의 위상 정렬 결과가 된다.
  - ```makeGraph()```함수가 이를 구현하는 모습을 볼 수 있다.
**2. 인접한 단어들만 검사하기**
- 만약 세 단어 A, B, C가 순서대로 등장한다면, (A,C)는 검사하지 않고 (A,B), (B,C)만 검사해도 답을 해결할 수 있다.
  - 예를 들어, A = impr, C = impo 라고 할때, B는 다음과 같이 3가지의 경우가 있다.
  1. B의 네 번째 글자가 'r'인 경우: (B,C)를 검사할 때 'r'이 'o'보다 앞에 온다는 사실을 알 수 있다.
  2. B의 네 번째 글자가 'o'인 경우: (A,B)를 검사할 때 'r'이 'o'보다 앞에 온다는 사실을 알 수 있다.
  3. B의 네 번째 글자가 'r', 'o' 둘다 아닌 경우: (A,B)를 검사할 때 'r'이 이 글자보다 앞에 온다는 사실을 알 수 있고, (B,C)를 검사할 때 이 글자가 'o'
    보다 앞에 온다는 사실을 알 수 있다. 결과적으로 'r'은 'o'보다 앞에 온다.
**3. 위상 정렬 적용**
- 위의 규칙을 적용하여 그래프를 생성한 뒤, 해당 그래프가 사이클이 없다면 문제의 사전은 논리에 맞는 것으로 알파벳 순서를 정상적으로 만들 수 있다.

## 구현 코드
```
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;
#define min(a,b) ((a) < (b) ? (a) : (b))


//알파벳의 각 글자에 대한 인접 행렬 표현
//간선 (i, j)는 알파벳 i가 j보다 앞에 와야 함을 나타낸다.
vector<vector<int>> adj;
//주어진 단어들로부터 알파벳 간의 선후관계 그래프를 생성한다.
void makeGraph(const vector<string> &words) {
	adj = vector<vector<int>>(26, vector<int>(26, 0));
	for (int j = 1; j < words.size(); ++j) {
		int i = j - 1, len = min(words[i].size(), words[j].size());
		//word[i]가 word[j]앞에 오는 이유를 찾는다.
		for (int k = 0; k < len; ++k)
			if (words[i][k] != words[j][k]) {
				int a = words[i][k] - 'a';
				int b = words[j][k] - 'a';
				adj[a][b] = 1;
				break;
			}
	}
}

vector<int> seen, order;
void dfs(int here) {
	seen[here] = 1;
	for (int there = 0; there < adj.size(); ++there)
		if (adj[here][there] && !seen[there])
			dfs(there);
	order.push_back(here);
}

//adj에 주어진 그래프를 위상정렬한 결과를 반환한다.
//그래프가 DAG가 아니라면 빈 벡터를 반환한다.
vector<int> topologicalSort() {
	int n = adj.size();
	seen = vector<int>(n, 0);
	order.clear();
	for (int i = 0; i < n; ++i)
		if (!seen[i])
			dfs(i);
	reverse(order.begin(), order.end());
	//만약 그래프가 DAG가 아니라면 정렬 결과에 역방향 간선이 있다.
	for (int i = 0; i < n; ++i)
		for (int j = i + 1; j < n; ++j)
			if (adj[order[j]][order[i]])
				return vector<int>();
	//없는 경우라면 깊이 우선 탐색에서 얻은 순서를 반환한다.
	return order;
}

int main()
{
	int tc; scanf("%d", &tc);
	while (tc--) {
		int n; scanf("%d", &n);
		vector<string> sv;
		for (int i = 0; i < n; i++) {
			string str; cin >> str;
			sv.push_back(str);
		}
		makeGraph(sv);
		vector<int> ans;
		ans = topologicalSort();
		if (!ans.size())
			printf("INVALID HYPOTHESIS");
		else {
			for (int i = 0; i < ans.size(); ++i)
				printf("%c", ans[i] + 'a');
		}
		printf("\n");
	}

	return 0;
}
```

[HOME](https://codemcd.github.io/)
