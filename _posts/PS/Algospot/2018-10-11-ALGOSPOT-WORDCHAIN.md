---
title: "[ALGOSPOT]WORDCHAIN"
search: true
categories:
  - PS
tags:
  - Algospot
---

# 단어 제한 끝말잇기

## 문제 링크
- [https://algospot.com/judge/problem/read/WORDCHAIN](https://algospot.com/judge/problem/read/WORDCHAIN)

## 코드 분석
### 1. 해밀토니안 경로(Hamiltonian path)
- 그래프의 모든 정점을 정확히 한 번씩 지나는 경로
- 해밀토니안 경로를 찾는 유일한 방법은 조합 탐색으로, 모든 정점의 배열을 하나하나 시도하며 이들이 경로가 되는지 확인하는 것이다.
  - 시간 복잡도: 최악의 경우 ```O(N!)```이다.
  - 이 문제 최대 크기인 100인 경우 ```100!```이므로, 시간안에 절대 해결할 수 없다.
- 예제 그림

![wordchain1](https://user-images.githubusercontent.com/34755287/44067837-560a5d24-9fb2-11e8-86b2-ba00d44078ef.JPG)

- 구현 코드

```
#define N 101
bool check[N];
int adj[N][N];
vector<string> sv, ans;
int n;

//행의 마지막 글자가 열의 첫 번째 글자와 일치하면 1값을 넣어,
//각각 단어를 이을 수 있는 지를 그래프로 표현한다.(인접 행렬)
void makeAdj() {
	for (int i = 0; i < n; ++i)
		for (int j = 0; j < n; ++j)
			if (i != j) {
				char iBackCh = sv[i][(int)sv[i].size() - 1];
				char jFrontCh = sv[j][0];
				if (iBackCh == jFrontCh)
					adj[i][j] = 1;
			}
}

//단어 정점을 하나씩 방문하며 경로가 되는지 검사한다.(DFS)
bool dfs(int here) {
	check[here] = true;
	ans.push_back(sv[here]);

	bool flag = true;
	for (int j = 0; j < n; ++j)
		if (!check[j]) {
			flag = false;
			break;
		}
	if (flag) return true;

	for (int there = 0; there < n; ++there) {
		if (!check[there] && adj[here][there]) {
			if (dfs(there)) return true;
			check[there] = false;
		}
	}
	return false;
}

//모든 정점에서 경로를 만들 수 있는지 검사한다.
bool dfsAll() {
	makeAdj();
	for (int i = 0; i < n; ++i) {
		memset(check, false, sizeof(check));
		ans.clear();
		if (dfs(i))
			return true;
	}
	return false;
}
```

### 2. 오일러 트레일 혹은 서킷
- 입력으로 주어진 각 단어를 정점이 아닌 간선으로 갖는 방향 그래프를 만든다.
- 각 정점은 알파벳으로 이루어져 있고, 각 단어의 첫글자에서 마지막 글자로 가는 간선을 만든다.

![wordchain2](https://user-images.githubusercontent.com/34755287/44067839-5638e540-9fb2-11e8-86a2-ed8191c53558.JPG)

- 위 그림에서와 같은 그래프에서 오일러 트레일 혹은 서킷을 활용하면, 답이 될 수 있는 것을 알 수 있다.
- 오일러 서킷 및 트레일의 자세한 내용은 링크: (추가 예정)
- 방향 그래프에서의 오일러 서킷
  - 무향 그래프에서는 각 정점에서ㅓ 인접하나 간선이 짝수 개여야 하지만, 방향 그래프에서는 둘 중 한 방향만 쓸 수 있기 때문에 **각 정점에 들어오는 간선의 수와 나가는 간선의 수가 같아야 한다.**
  - 방향 그래프에서 오일러 서킷을 통해 오일러 트레일(a에서 시작, b에 도착)을 찾는 방법
    - 간선 (b, a)를 추가 한다.
    - 정점 a에서는 나가는 간선이 들어오는 간선보다 하나 많고, 정점 b는 들어오는 간선이 나가는 간선보다 하나 많아야 한다.
    - 위 두 정점을 제외한 나머지 정점은 나가는 간선과 들어오는 간선의 수가 같아야 한다.
- 구현 코드
```
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

/* 끝말잊기 문제의 입력을 그래프로 만들기*/
//그래프의 인점 행렬 표현, adj[i][j] = i와 j사이의 간선의 수
vector<vector<int>> adj;
//graph[i][j] = i로 시작해서 j로 끝나는 단어의 수
vector<string> graph[26][26];
//indegree[i] = i로 시작하는 단어의 수
//outdegree[i] = i로 끝나는 단어의 수
vector<int> indegree, outdegree;
void makeGraph(const vector<string> &words) {
	//전역 변수 초기화
	for (int i = 0; i < 26; ++i)
		for (int j = 0; j < 26; ++j)
			graph[i][j].clear();
	adj = vector<vector<int>>(26, vector<int>(26, 0));
	indegree = outdegree = vector<int>(26, 0);
	//각 단어를 그래프에 추가한다.
	for (int i = 0; i < words.size(); ++i) {
		int a = words[i][0] - 'a';
		int b = words[i][words[i].size() - 1] - 'a';
		graph[a][b].push_back(words[i]);
		adj[a][b]++;
		outdegree[a]++;
		indegree[b]++;
	}
}

/* 방향 그래프에서 오일러 서킷 혹은 트레일 찾아내기 */
//유향 그래프의 인접 행렬 adj가 주어질 때 오일러 서킷 혹은 트레일을 계산한다.
void getEulerCircuit(int here, vector<int> &circuit) {
	for (int there = 0; there < adj.size(); ++there)
		while (adj[here][there] > 0) {
			adj[here][there]--;  //간선을 지운다
			getEulerCircuit(there, circuit);
		}
	circuit.push_back(here);
}

//현재 그래프의 오일러 트레일이나 서킷을 반환한다.
vector<int> getEulerTrailOrCircuit() {
	vector<int> circuit;
	//우선 트레일을 찾아본다: 시작점이 존재하는 경우
	for(int i = 0; i < 26; ++i)
		if (outdegree[i] == indegree[i] + 1) {
			getEulerCircuit(i, circuit);
			return circuit;
		}
	//아니면 서킷이니, 간선에 인접한 아무 정점에서나 시간한다.
	for(int i = 0; i < 26; ++i)
		if (outdegree[i]) {
			getEulerCircuit(i, circuit);
			return circuit;
		}
	//모두 실패한 경우 빈 배열을 반환한다.
	return circuit;
}

/* 끝말잇기 문제를 오일러 트레일 문제로 바꿔 해결하는 알고리즘 */

string solve(const vector<string> &words) {
	makeGraph(words);
	//오일러 서킷이나 트레일으르 찾아낸다.
	vector<int> circuit = getEulerTrailOrCircuit();
	//모든 간선을 방문하지 못했으면 실패
	if (circuit.size() != words.size() + 1) return "IMPOSSIBLE";
	//아닌 경우 방문 순서를 뒤집은 뒤 간선들을 모아 문자열로 만들어 반환한다.
	reverse(circuit.begin(), circuit.end());
	string ret;
	for (int i = 1; i < circuit.size(); ++i) {
		int a = circuit[i - 1], b = circuit[i];
		if (ret.size()) ret += " ";
		ret += graph[a][b].back();
		graph[a][b].pop_back();
	}
	return ret;
}

int main()
{
	int tc; scanf("%d", &tc);
	while (tc--) {
		vector<string> sv;
		int n;  scanf("%d", &n);
		for (int i = 0; i < n; ++i) {
			string str;
			cin >> str;
			sv.push_back(str);
		}
		cout << solve(sv) << endl;
	}

	return 0;
}
```

## 예외 사항
- 위 오일러 서킷 및 트레일 코드에 예외 사항이 발생한다.
  - 결과는 정답으로 나온다.
- ```getEulerCircuit()```함수에서 갈 수 없는 경로를 가는 경우가 발생하여 vector에 추가하지 못하는데, ```solve()```함수에서 정답을 출력하려 하기 때문에 존재하지 않는 vector 인덱스에 접근하여 오류로 강제 종료된다.
- 예외 테스트 케이스
```
1
5
god
dog
gun
new
nurse
```
- 나중에 수정 및 해결 해볼것...

[HOME](https://codemcd.github.io/)
