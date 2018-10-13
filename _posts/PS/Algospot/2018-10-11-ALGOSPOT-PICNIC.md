---
title: "[ALGOSPOT]PICNIC"
excerpt: "완전 탐색"
search: true
categories:
  - PS
tags:
  - Algospot
---

# PICNIC

## 문제 링크
- [https://algospot.com/judge/problem/read/PICNIC](https://algospot.com/judge/problem/read/PICNIC)

## 코드 분석
### 논리 분석
- 입력받은 친구가 가능한 쌍을 2차원 배열에 중복없이 저장한다. ((1,0), (0,1)도 중복으로 처리한다.)
- 경우의 수를 체크하기 위해 가능한 쌍의 최대 크기(45)만큼 가로, 세로 크기를 갖는 2차원 배열을 선언한다.
- 모든 친구들이 쌍을 이루었는지 체크하는 1차원 배열을 선언한다.
- 최대 시간복잡도는 O(m^2)로 예상한다. (최대 반복 수는 45 * (45 + 45) 로 예사됨)

### 구현 코드

~~~cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
using namespace std;
#define N 11  //최대 친구 수
#define M 46  //최대 친구 쌍의 수
bool checkF[N];  //모든 친구들이 짝이 만들어졌는지 체크
bool checkP[M][M];  //짝지을 수 있는 방법을 저장할 배열(중복되지 않음)
int _pair[M][2];   //가능한 친구 쌍을 저장할 배열
bool tmp[M];  //짝지을 방법를 임시로 저장할 배열

void init_checkF_tmp(void)
{
	for (int i = 0; i < N; i++)
		checkF[i] = false;
	for (int i = 0; i < M; i++)
		tmp[i] = false;
}

void init_pair(void)
{
	for (int i = 0; i < M; i++) {
		_pair[i][0] = 0;
		_pair[i][1] = 0;
		for (int j = 0; j < M; j++)
			checkP[i][j] = false;
	}
}

bool full_checkF(int n)
{
	for (int i = 0; i < n; i++) {
		if (!checkF[i])
			return false;
	}
	return true;
}

//n은 현재 방법의 수
bool check_pair(int n, int m)
{
	bool flag = false;
	for (int i = 0; i < n; i++) {
		for (int j = 0; j < m; j++) {
			//하나라도 다른 부분이 있다면 다음 방법으로 넘어가서 검사한다.
			if (tmp[j] == checkP[i][j])
				flag = true;
			else {
				flag = false;
				break;
			}
		}
		//완전히 같으면 FALSE 반환
		if (flag) return false;
	}
	return true;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		init_pair();

		int n, m;
		scanf("%d %d", &n, &m);
		bool repet_flag = false;
		int repet_cnt = 0;
		for (int i = 0; i < m; i++) {
			int f, s;
			scanf("%d %d", &f, &s);
			//(0, 1), (1, 0)과 같은 중복을 방지하기 위해 서로 같은 쌍은 입력하지 않는다.
			repet_flag = false;
			for (int j = 0; j < i; j++) {
				if (_pair[j][0] == f && _pair[j][1] == s) repet_flag = true;
				if (_pair[j][0] == s && _pair[j][1] == f) repet_flag = true;
			}
			if (repet_flag)
				repet_cnt++;
			else {
				_pair[i - repet_cnt][0] = f;
				_pair[i - repet_cnt][1] = s;
			}
		}
		m -= repet_cnt;

		int ans = 0;
		for (int i = 0; i < m; i++) {
			init_checkF_tmp();
			checkF[_pair[i][0]] = true;
			checkF[_pair[i][1]] = true;
			tmp[i] = true;
			for (int j = 0; j < m; j++) {
				//중복을 피해기 위함
				if (i == j)
					continue;
				//친구쌍의 두 친구가 모두 check배열에 false면 해당 쌍을 사용한다.(중복검사)
				if (!checkF[_pair[j][0]] && !checkF[_pair[j][1]]) {
					checkF[_pair[j][0]] = true;
					checkF[_pair[j][1]] = true;
					tmp[j] = true;
				}
			}
			//모든 친구들이 짝을 이루었고, 이전에 방법과 다르다면 새로운 방법이다.
			if (full_checkF(n) && check_pair(ans, m)) {
				for (int j = 0; j < m; j++)
					checkP[ans][j] = tmp[j];
				ans++;
			}
		}
		printf("%d\n", ans);
	}

	return 0;
}
~~~

### 결과
- 수행시간 : 0ms
- 오답
- 디버깅과 다른 예제를 만들어서 테스트한 결과 틀린 부분을 찾을 수 없었다.

### 알아둘 점
- 재귀에 관한 이해가 더욱 필요하다.
- 위의 문제도 책의 해답에서는 재귀를 사용하였다.

## 해답
### 코드
- 출처 : 알고리즘 문제해결전략 책

~~~cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
using namespace std;
#define N 10
int n;
bool areFriends[N][N];
//taken[i] = i번째 학생이 짝을 이미 찾았으면 true, 아니면 false
int countPairings(bool taken[N])
{
	//남은 학생들 중 가장 번호가 빠른 학생을 찾는다.
	int firstFree = -1;
	for (int i = 0; i < n; i++) {
		if (!taken[i]) {
			firstFree = i;
			break;
		}
	}
	//기저 사례 : 모든 학생이 짝을 찾았으면 한 가지 방법을 찾았으니 종료한다.
	if (firstFree == -1) return 1;
	int ret = 0;
	//이 학생과 짝지을 학생을 결정한다.
	for (int pairWith = firstFree + 1; pairWith < n; pairWith++) {
		if (!taken[pairWith] && areFriends[firstFree][pairWith]) {
			taken[firstFree] = taken[pairWith] = true;
			ret += countPairings(taken);
			//모든 경우의 수를 구해야하기 때문에 초기화해야함
			taken[firstFree] = taken[pairWith] = false;
		}
	}
	return ret;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		for (int i = 0; i < N; i++)
			for (int j = 0; j < N; j++)
				areFriends[i][j] = false;
		int m;
		scanf("%d %d", &n, &m);
		for (int i = 0; i < m; i++) {
			int f, s;
			scanf("%d %d", &f, &s);
			areFriends[f][s] = areFriends[s][f] = true;
		}
		bool taken[10] = { false, };
		int ans = countPairings(taken);
		printf("%d\n", ans);
	}

	return 0;
}
~~~

### 결과 및 코드 분석
- 수행 시간 : 0ms
- 최대 시간 복잡도 : 열 명의 학생이 모두 친구인 경우(9 * 7 * 5 * 3 * 1 = 945)
- 재귀 함수 사용
- 공간 복잡도면에서, 2차원 배열 1개와 1차원 배열 1개를 사용하여 내가 구현한 코드보다 더 효율적이다.
- 친구 쌍이 가능한지를 저장하는 2차원 배열에서 그 반대의 경우도 모두 저장해주어야 정확한 답이 나온다.

### 알아둘 점
**1) 재귀 함수 구현 능력**
</br>**2) 재귀 함수를 사용하기 위한 입력 형식을 만드는 방법**
