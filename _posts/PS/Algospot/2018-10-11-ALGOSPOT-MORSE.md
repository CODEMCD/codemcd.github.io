---
title: "[ALGOSPOT]MORSE"
excerpt: "DP"
search: true
sidebar_main: true
categories:
  - PS
tags:
  - Algospot
---

# 모스 부호 사전

## 문제 링크
- K번째 답 계산하기
- [https://algospot.com/judge/problem/read/MORSE](https://algospot.com/judge/problem/read/MORSE)

## 코드 분석
### 1. 완전 탐색
- 장점인 신호가 단점인 신호보다 사전순으로 항상 앞으로 오는 모든 신호 만들기

```cpp
//s: 지금까지 만든 신호
//n: 더 필요한 '-'의 개수
//m: 더 필요한 'o'의 개수
void generate(int n, int m, string s) {
	//기저 사례: n = m = 0
	if (n == 0 && m == 0) {
		if (skip == 0) cout << s << endl;
		--skip;
		return;
	}
	if (bino[n + m][n] <= skip) {
		skip -= bino[n + m][n];
		return;
	}
	if (n > 0) generate(n - 1, m, s + "-");
	if (m > 0) generate(n, m - 1, s + "o");
}
```

- k - 1개 건너뛰기

```cpp
//skip개를 건너뛰고 출력한다.
void generate(int n, int m, string s) {
	//기저 사례: skip < 0 (skip=-1이란 것은 이미 k번째 신호가 출력됨)
	if (skip < 0) return;
	//기저 사례: n = m = 0
	if (n == 0 && m == 0) {
		//더 건너뛸 신호가 없는 경우
		if (skip == 0) cout << s << endl;
		--skip;
		return;
	}
	if (bino[n + m][n] <= skip) {
		skip -= bino[n + m][n];
		return;
	}
	if (n > 0) generate(n - 1, m, s + "-");
	if (m > 0) generate(n, m - 1, s + "o");
}
```

- 전역 변수 skip은 k - 1로 초기화해야 한다.
- 시간 복잡도
  - n, m의 최대 크기는 각각 100이다.
  - 모든 경우의 수는 이항 계수 ```C(n + m, n)```이므로 ```C(200, 100)```이다. 이는 시간내에 절대 계산할 수 없다.

### 2. 동적 계획법
- 모든 신호를 만드는 경우의 수는 ```C(n + m. n)```이다.
- skip이 ```C(n + m. n)```와 같거나 크다면 ```generate(n, m, s)```가 종료될 때, skip은 ```C(n + m. n)```만큼 줄어 있고 답은 못찾은 상태이다.
- 그렇다면, 함수를 실행할 필요 없이 skip만 줄여 버리고 종료해도 똑같은 결과가 나온다.
- skip 변수의 오버플로를 막기 위해 문제에서 k가 항상 10억 이하라는 점을 이용한다.
  - 이항계수 계산을 ```C(n, r)```대신 ```min( C(n, r), 10^9 )```으로 한다.
- 시간 복잡도
  - ```generate()``` 함수는 k번째 신호 외에는 어떤 신호도 만들지 않는다. => ```O(n + m)```
  - ```calcBino()``` 함수는 이항 계수를 미리 모두 계산하는 함수이다. => ```O(nm)```
  - 결과적으로, 총 시간 복잡도는 ```O(nm)```이다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <string>
using namespace std;
#define min(a,b) ((a) < (b) ? (a) : (b))
#define N 201
//k의 최대값  + 100, 오버플로를 막기 위해 이보다 큰 값은 구하지 않는다.
const int M = 1000000000 + 100;
int bino[N][N];
int skip;

//필요한 모든 이항계수를 미리 계산해 둔다.
void calcBino() {
	memset(bino, 0, sizeof(bino));
	for (int i = 0; i < N; i++) {
		bino[i][0] = bino[i][i] = 1;
		for (int j = 1; j < i; j++)
			bino[i][j] = min(M, bino[i - 1][j - 1] + bino[i - 1][j]);
	}
}

//s: 지금까지 만든 신호
//n: 더 필요한 '-'의 개수
//m: 더 필요한 'o'의 개수
//skip개를 건너뛰고 출력한다.
void generate(int n, int m, string s) {
	//기저 사례: skip < 0 (skip=-1이란 것은 이미 k번째 신호가 출력됨)
	if (skip < 0) return;
	//기저 사례: n = m = 0
	if (n == 0 && m == 0) {
		//더 건너뛸 신호가 없는 경우
		if (skip == 0) cout << s << endl;
		--skip;
		return;
	}
	if (bino[n + m][n] <= skip) {
		skip -= bino[n + m][n];
		return;
	}
	if (n > 0) generate(n - 1, m, s + "-");
	if (m > 0) generate(n, m - 1, s + "o");
}

int main()
{
	int tc; scanf("%d", &tc);
	calcBino();
	while (tc--) {
		int n, m, k; scanf("%d %d %d", &n, &m, &k);
		string s;
		//skip 초기화
		skip = k - 1;
		generate(n, m, s);
	}

	return 0;
}
```
