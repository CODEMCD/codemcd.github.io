---
title: "[ALGOSPOT]BOARDCOVER"
search: true
categories:
  - PS
tags:
  - Algospot
---

# BOARDCOVER

## 문제 링크
- [https://algospot.com/judge/problem/read/BOARDCOVER](https://algospot.com/judge/problem/read/BOARDCOVER)

## 내 코드 분석
### 논리 분석
- 한 칸에 8개의 L자 모양을 만들 수 있다.
- 재귀 함수를 이용하여, 전체 경우의 수를 계산한다.
- 하지만, 아직 재귀 함수가 익숙하지 않아 완성하지 못하였다.
- 재귀 함수 공부를 위해 다른 방법은 생각하지 않았다.

## 해답
### 논리 분석
- 재귀 호출의 각 단계마다 아직 흰 칸중에서 가장 윗 줄, 가장 왼쪽 칸부터 덮도록 규칙을 정한다.
- 위 규칙을 적용하면, 위에서 정한 칸 이전은 모두 채워졌다는 것을 알 수 있으므로 내가 생각했던 8가지 모양이 아니라 4가지 모양이 나온다. (아래 그림 참고)

![boardcover_l](https://user-images.githubusercontent.com/34755287/40338256-100041bc-5daf-11e8-98fb-4ecea329cc0a.png)

### 코드
- 출처 : 알고리즘 문제해결전략 책
~~~
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <vector>
using namespace std;

//주어진 칸을 덮을 수 있는 네 가지 방법
//블록을 구성하는 세 칸의 상대적 위치 (dy, dx)의 목록(위 그림 참고)
const int coverType[4][3][2] = {
	{ { 0, 0 },{ 1, 0 },{ 0, 1 } },
	{ { 0, 0 },{ 0, 1 },{ 1, 1 } },
	{ { 0, 0 },{ 1, 0 },{ 1, 1 } },
	{ { 0, 0 },{ 1, 0 },{ 1, -1 } }
};
//board의 (y, x)를 type번 방법으로 덮거나, 덮었던 블록을 없앤다.
//delta = 1이면 덮고, -1이면 덮었던 블록을 없앤다.
//만약 블록이 제대로 덮이지 않은 경우(게임판 밖으로 나가거나, 겹쳐지거나, 검은 칸일 때) false를 반환한다.
bool set(vector<vector<int>>&board, int y, int x, int type, int delta)
{
	bool ok = true;
	for (int i = 0; i < 3; i++) {
		const int ny = y + coverType[type][i][0];
		const int nx = x + coverType[type][i][1];
		if (ny < 0 || ny >= (int)board.size() || nx < 0 || nx >= (int)board[0].size())
			ok = false;
		else if ((board[ny][nx] += delta) > 1)
			ok = false;
	}
	return ok;
}

//board의 모든 빈 칸을 덮을 수 있는 방법의 수를 반환한다.
//board[i][j] = 1 이미 덮인 칸 혹은 검은 칸
//board[i][j] = 0 아직 덮이지 않은 칸
int cover(vector<vector<int>>&board)
{
	//아직 채우지 못한 칸 중 가장 윗줄 왼쪽에 있는 칸을 찾는다.
	int y = -1, x = -1;  //flag 기능을 추가하기 위해 -1로 초기화
	for (int i = 0; i < (int)board.size(); i++) {
		for (int j = 0; j < (int)board[i].size(); j++) {
			if (board[i][j] == 0) {
				y = i;
				x = j;
				break;
			}
		}
		if (y != -1) break;
	}
	//기저 사례: 모든 칸을 채웠으면 1을 반환한다.
	if (y == -1) return 1;
	int ret = 0;
	for (int type = 0; type < 4; type++) {
		//만약 board[y][x]를 type 형태로 덮을 수 있으면 재귀 호출한다.
		if (set(board, y, x, type, 1))
			ret += cover(board);
		//덮었던 블록을 치운다.
		set(board, y, x, type, -1);
	}
	return ret;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		vector<vector<int>> board;
		vector<int> v;
		char row[21];
		int h, w;
		scanf("%d %d", &h, &w);
		for (int i = 0; i < h; i++) {
			scanf("%s", row);
			for (int j = 0; j < w; j++) {
				if (row[j] == '#')
					v.push_back(1);
				else
					v.push_back(0);
			}
			board.push_back(v);
			v.clear();
		}
		/*for (int i = 0; i < (int)board.size(); i++) {
			for (int j = 0; j < (int)board[i].size(); j++) {
				printf("%d ", board[i][j]);
			}
			printf("\n");
		}*/

		printf("%d\n", cover(board));
	}

	return 0;
}
~~~
### 결과 및 코드 분석
- 수행 시간: 0ms
- 최대 시간 복잡도: 블록을 하나 놓을 때마다 4가지 선택지가 있다. 최대 16개의 블록을 놓기 때문에 가능한 최대 연산 수는 4^16(2^32)으로
시간내에 불가능하게 느껴진다. 하지만 6칸이 있다고 가정하면, 4^2으로 16가지 방법이 있어야 하는데 실제로 계산하면 2가지 방법밖에 없다.
이렇게 프로그램을 작성하고 직접 여러 예제를 넣어 이론과 다른 실제 시간복잡도를 계산할 수 있어야 한다.

### 알아둘 점
1) 재귀를 구현하는 논리 흐름
2) 이중 Vector 함수
- 사용법 (위 main함수 참고)
~~~
vector<vector<int>> board;
vector<int> v;
char row[21];
int h, w;
scanf("%d %d", &h, &w);
for (int i = 0; i < h; i++) {
		scanf("%s", row);
		for (int j = 0; j < w; j++) {
			if (row[j] == '#')
				v.push_back(1);
			else
				v.push_back(0);
		}
		board.push_back(v);
		v.clear();
}
~~~
- 장점
  - 크기를 지정하지 않고 입력을 줄 수 있다.
  - STL이기 때문에 효율성을 보장받을 수 있다.


[HOME](https://codemcd.github.io/)
