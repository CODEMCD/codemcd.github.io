---
title: "[ALGOSPOT]BOGGLE"
search: true
categories:
  - PS
tags:
  - Algospot
---

# BOGGLE

## 문제 링크
- [https://algospot.com/judge/problem/read/BOGGLE](https://algospot.com/judge/problem/read/BOGGLE)

## 내 코드 분석
### 논리 분석

- char 형인 알파벳을 아스키 코드값을 이용하여 int형으로 사용한다.
- check 배열을 선언하여 5 x 5 배열입력에서 사용된 알파벳을 저장하여 문자 입력 시 5 x 5 배열에 없는 알파벳이 있을 경우 바로 'NO'를 출력한다.
(시간복잡도를 줄이기 위함)
- BFS를 활용하여 문자가 5 x 5배열에서 만들어질 수 있는지 판단한다.
- check_cnt 배열은 BFS 수행과정에서 해당 문자의 다음 알파벳을 비교하기 위해 수행과정에서의 문자 길이를 갱신한다.

### 구현 코드
~~~
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>
using namespace std;
#define N 26
bool check[N];  // 5 x 5 맵에 입력된 알파벳 종류 확인
int check_cnt[5][5];  // BFS에서 다음 문자 알파벳을 판단하기 위한 2차원 배열
int map[5][5];  // 5 x 5 알파벳을 입력할 2차원 배열
int word[11];  // 문자를 저장할 배열
int mx[8] = { 0, 1, 1, 1, 0, -1, -1, -1 };
int my[8] = { -1, -1, 0, 1, 1, 1, 0, -1 };

void init(void)
{
	for (int i = 0; i < 5; ++i)
		for (int j = 0; j < 5; ++j)
			map[i][j] = 0;
	for (int i = 0; i < N; ++i)
		check[i] = false;
	for (int i = 0; i < 11; ++i)
		word[i] = -1;
	for (int i = 0; i < 5; ++i)
		for (int j = 0; j < 5; ++j)
			check_cnt[i][j] = 0;
}

void init_cnt(void)
{
	for (int i = 0; i < 5; ++i)
		for (int j = 0; j < 5; ++j)
			check_cnt[i][j] = 0;
}

int main(void)
{
	int tc;
	cin >> tc;
	//scanf("%d", &tc);
	//while (getchar() != '\n');  //입력 버퍼에서 엔터 지우기

	while (tc--) {
		init();  //다음 테스트 케이스를 위한 초기화

		for (int i = 0; i < 5; ++i) {  // 5 x 5 알파벳 배열 입력
			for (int j = 0; j < 5; ++j) {
				char alph;
				//scanf("%c", &alph);
				cin >> alph;
				map[i][j] = alph - 'A';  //char -> int 로 사용하기 위함
				if (!check[alph - 'A'])  
					check[alph - 'A'] = true;
			}
			//while (getchar() != '\n');
		}

		int n;
		cin >> n;
		//scanf("%d", &n);
		//while (getchar() != '\n');

		int str_len;  //단어 길이
		bool flag = false;  // 단어를 맵에서 찾을 수 있으면 TRUE, 못 찾으면 FALSE
		pair<int, int> p;  //큐에 2차원 배열 인덱스를 저장하기 위한 pair
		queue<pair<int, int>> q;  //BFS를 위한 큐
		for (int i = 0; i < n; ++i) {
			for (int ii = 0; ii < 11; ++ii)  //단어 입력 초기화
				word[ii] = -1;
			flag = false;
			char inputW[11];
			cin >> inputW;
			//scanf("%s", inputW);
			//while (getchar() != '\n');
			str_len = strlen(inputW);
			for (int j = 0; j < str_len; ++j)
				word[j] = inputW[j] - 'A';

			for (int j = 0; j < str_len; ++j) {  //map에 알파벳이 없을 때 바로 NO출력하고 다음 단어
				if (!check[word[j]]) {
					for (int l = 0; l < str_len; ++l)
						printf("%c", word[l] + 'A');
					printf(" NO\n");
					flag = true;
				}
				if (flag) break;
			}
			if (str_len == 1 && check[word[0]]) {  //문자 길이가 1일 때 처리
				for (int ll = 0; ll < str_len; ++ll)
					printf("%c", word[ll] + 'A');
				printf(" YES\n");
				flag = true;
			}
			if (flag) continue;  //문자 판단이 끝났으므로 다음으로 넘어감

			flag = false;
			for (int j = 0; j < 5; ++j) {
				for (int k = 0; k < 5; ++k) {
					if (map[j][k] == word[check_cnt[j][k]]) {  // 5 x 5 배열중 문자의 첫 알파벳 위치
						p = make_pair(j, k);
						q.push(p);  //큐에 삽입

						while (!q.empty()) {
							int nowx = q.front().second;
							int nowy = q.front().first;
							q.pop();

							for (int l = 0; l < 8; ++l) {  // 8방향으로 모두 체크
								int nextx = nowx + mx[l];
								int nexty = nowy + my[l];

								if (!flag && nextx >= 0 && nextx < 5 && nexty >= 0 && nexty < 5) {  // 5 x 5 배열 밖으로 나갔는지 체크
									if (word[check_cnt[nowy][nowx] + 1] != -1 && map[nexty][nextx] == word[check_cnt[nowy][nowx] + 1]) {  // 다음 알파벳과 일치하면
										check_cnt[nexty][nextx] = check_cnt[nowy][nowx] + 1;  // 현재 문자열 길이 갱신
										if ((check_cnt[nexty][nextx] + 1) == str_len) {  // 현재 체크중인 문자열 길이와 최종 문자열 길이가 일치하면 YES출력
											flag = true;
											for (int ll = 0; ll < str_len; ++ll)
												printf("%c", word[ll] + 'A');
											printf(" YES\n");
											break;
										}
										p = make_pair(nexty, nextx);
										q.push(p);
									}
								}
							}
							if (flag) break;
						}
					}
					init_cnt();
					while (!q.empty()) q.pop();  //정답을 제출하고 바로 반복문을 빠져나갔기 때문에 큐에 남은 인덱스가 있을 수 있기 때문
					if (flag) break;
				}
				if (flag) break;
			}

			if (!flag) {  //못찾으면 NO 출력
				for (int l = 0; l < str_len; ++l)
					printf("%c", word[l] + 'A');
				printf(" NO\n");
			}
		}
	}

	return 0;
}
~~~

### 결과
- 수행 시간 : 4ms
- 오답
- 디버깅과 직접 그리면서 코드를 분석하였지만 틀린 부분을 찾을 수 없었다.
- 위의 참고 예제를 통과하면 왠만한 예제는 통과하였다고 생각한다.
- 다른 의견
  - 재귀를 활용하여 구현하면 시간 초과가 난다고 한다.
  - 다이나믹 프로그래밍으로 구현하는 것이 가장 효율적이라고 생각한다.

### 알아둘 점
**1) 입력 버퍼 지우기**
~~~
while (getchar() != '\n');
~~~
 - 문자열을 입력할 때 입력버퍼에 엔터키(\n)가 남아있는 현상으로 다음 문자열을 입력할 때 제대로된 입력을 받을 수 없었다.
 - 입력함수 바로 밑에 위의 코드를 추가하여 해결하였다.
 - cin 을 사용하여 해결할 수도 있다.

<br>**2) BFS에서 경로 순서 확인하기**
- BFS는 큐를 활용하기 때문에 경로를 찾아가는 것이 순차적으로 하기 않는다.
- 위의 이유로 똑같은 크기의 2차원 배열을 선언하여 이전의 값에 +1을 하며 해당 배열에 저장한다.
- 위의 코드에서는 check_cnt 배열이 그 역할을 한다.

## 해답
### 코드
- 출처 : http://b.mystika.me/26
~~~
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

char map[5][5];
char word[11];
int dp[5][5][10];
int mx[8] = { 0,1,1,1,0,-1,-1,-1 };
int my[8] = { -1,-1,0,1,1,1,0,-1 };

int solve(int y, int x, int idx)
{
	int &ret = dp[y][x][idx];
	//초기화 오류 처리
	if (ret != -1) return ret;
	for (int i = 0; i < 8; i++) {
		int nexty = y + my[i];
		int nextx = x + mx[i];

		if (nexty >= 0 && nexty < 5 && nextx >= 0 && nextx < 5) {
			//단어와 일치되는 알파벳을 찾았을 때,
			if (map[nexty][nextx] == word[idx]) {
				//단어의 다음 문자가 NULL이면, 모두 찾았기 때문에 TRUE를 반환
				if (word[idx + 1] == '\0') return ret = 1;
				//아직 단어를 찾을 것이 남아있다면 다시 재귀함수 호출
				ret = solve(nexty, nextx, idx + 1);  
				//문자열을 찾았다면 남은 재귀함수에서 모두 1(TRUE)값을 반환하여 마지막 결과에 1을 반환하도록 한다.
				if (ret) return ret;
			}
		}
	}
	//문자를 모두 매칭할 수 없으므로 FALSE를 반환
	return false;
}

int find()
{
	for (int i = 0; i < 5; i++) {
		for (int j = 0; j < 5; j++) {
			int &ret = dp[i][j][0];
			//초기화가 안됬다면 바로 리턴(오류 처리)
			if (ret != -1) return ret;
			//첫 단어를 map에서 찾았다면
			if (map[i][j] == word[0]) {
				ret = solve(i, j, 1);
				if (ret) return ret;
			}
		}
	}
	return false;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		for (int i = 0; i < 5; i++)
			scanf("%s", map[i]);
		int n;
		scanf("%d", &n);
		for (int i = 0; i < n; i++) {
			memset(dp, -1, sizeof(dp));
			scanf("%s", word);
			printf("%s %s\n", word, find() ? "YES" : "NO");
		}
	}
	return 0;
}
~~~
- 출처를 참고하여 다시 구현해 본 코드이다.
- DP를 사용하였으며, dp[y][x][idx]는 해당 맵의 좌표(map[y][x])에서 현재 단어(word[idx]) 이후를 찾을 수 있는지를 판단한다.

### 알아둘 점
**1) 재귀함수를 이용하여 DP를 구현하는 방식**
<br>**2) 삼항 연산자를 이용하여 간단히 출력하는 방식**
 - 특히, 삼항 연산자를 사용하기 위해 함수를 사용한 부분을 참고하자.

[HOME](https://codemcd.github.io/)
