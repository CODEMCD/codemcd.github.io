---
title: "[SW Expert Academy]5650.핀볼 게임"
excerpt: "시뮬레이션"
search: true
categories:
  - PS
tags:
  - SW Expert Academy
---

# 핀볼 게임

## 문제 링크
- [https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRF8s6ezEDFAUo](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRF8s6ezEDFAUo)

## 해결 방법
### 시뮬레이션
- 각각의 벽돌이나 벽을 만났을 때 바뀌는 방향을 설정한다.
- 웜홀은 각 번호마다 2가지 좌표가 존재하므로 이를 번갈아 호출될 수 있도록 한다.
- 종료 조건은 블랙홀을 만났거나 다시 출발점으로 되돌아 온 경우이다.
  - 출발점으로 되돌아 온 경우가 종료 조건이므로 반드시 종료되는 것을 보장할 수 있다.
- 위의 조건을 바탕으로 게임판에서 공을 둘 수 있는 모든 좌표와 그 좌표의 4 방향을 모두 검사하여 가장 큰 점수를 계산한다.

## 시간 복잡도
- 게임판 최대 크기: ```100 * 100```
- 방향: ```4```
- 공을 둘 수 있는 모든 경우의 수: ```100 * 100 * 4 = 40,000```
  - 벽돌, 블랙홀, 웜홀이 존재하므로 이 수보다 적을 것이라 예상된다.
- 시뮬레이션의 시간 복잡도 계산을 확신 할 수 없다.
  - 최대는 ```100 * 100```인 것 같지만, 이렇게 될려면 벽돌 위치가 특정한 조건을 만족해야 한다.

## Notice
- 변수들을 갱신할 때, 계산의 중간 과정에서 초기 변수 값을 사용해야 한다면 그 값들을 임의의 저장소에 저장하여 사용해야 한다.
  - 그렇지 않으면, 중간에 갱신된 변수 값들을 다른 변수의 계산 과정에서 사용하면 전혀 다른 값이 나오게 된다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <algorithm>
#include <vector>
using namespace std;

int my[4] = { -1, 0, 1, 0 };
int mx[4] = { 0, 1, 0, -1 };
int map[101][101];
//웜홀 좌표 저장
pair<int, int> wormhole[5][2];
int wormCnt[5];

//현재 방향에서 블록에 부딫혔을때 바뀌는 방향을 반환한다.
int workBlock(int curDir, int blockNum) {
	if (blockNum == 1) {
		switch (curDir) {
		case 0: return 2;
		case 1: return 3;
		case 2: return 1;
		case 3: return 0;
		default: return -1;
		}
	}
	else if (blockNum == 2) {
		switch (curDir) {
		case 0: return 1;
		case 1: return 3;
		case 2: return 0;
		case 3: return 2;
		default: return -1;
		}
	}
	else if (blockNum == 3) {
		switch (curDir) {
		case 0: return 3;
		case 1: return 2;
		case 2: return 0;
		case 3: return 1;
		default: return -1;
		}
	}
	else if (blockNum == 4) {
		switch (curDir) {
		case 0: return 2;
		case 1: return 0;
		case 2: return 3;
		case 3: return 1;
		default: return -1;
		}
	}
	else if (blockNum == 5) {
		switch (curDir) {
		case 0: return 2;
		case 1: return 3;
		case 2: return 0;
		case 3: return 1;
		default: return -1;
		}
	}
	return -1;
}

void init() {
	for (int i = 0; i < 101; ++i)
		for (int j = 0; j < 101; ++j)
			map[i][j] = 0;
	for (int i = 0; i < 5; ++i)
		wormCnt[i] = 0;
}

int main()
{
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {
		init();

		int n; scanf("%d", &n);
		for (int i = 0; i < n; ++i)
			for (int j = 0; j < n; ++j) {
				scanf("%d", &map[i][j]);
				if (map[i][j] >= 6 && map[i][j] <= 10)
					wormhole[map[i][j] - 6][wormCnt[map[i][j] - 6]++] = make_pair(i, j);
			}

		int ans = 0;
		int curScore, y, x, dir;
		//공을 놓을 수 있는 모든 좌표를 탐색한다.
		for (int i = 0; i < n; ++i)
			for (int j = 0; j < n; ++j)
				//빈 공간이면 공을 놓을 수 있다.
				if (map[i][j] == 0) {
					//상하좌우 모든 방향
					for (int k = 0; k < 4; ++k) {
						y = i;
						x = j;
						curScore = 0;
						dir = k;
						//시뮬레이션
						for (int m = 0; m < n; ++m) {
							y += my[dir];
							x += mx[dir];
							// 벽을 만난 경우
							//인덱스가 게임밖으로 나가기 때문에 먼저 검사해야 한다.
							if (y < 0 || y >= n || x < 0 || x >= n) {
								dir = (dir + 2) % 4;
								curScore++;
								m = -1;
								continue;
							}
							// 종료 조건: 블랙홀을 만나거나 처음으로 되돌아 온 경우
							if (map[y][x] == -1 || (y == i && x == j)) {
								ans = ans > curScore ? ans : curScore;
								break;
							}
							// 웜홀을 만난 경우
							else if (map[y][x] >= 6 && map[y][x] <= 10) {
								//y가 변한 값으로 x를 계산과정에서 map 인덱스로 사용하므로
								//다른 변수에 저장해놓고 사용해야 한다.
								int nexty = y;
								int nextx = x;
								if (y == wormhole[map[nexty][nextx] - 6][0].first &&
									x == wormhole[map[nexty][nextx] - 6][0].second) {
									y = wormhole[map[nexty][nextx] - 6][1].first;
									x = wormhole[map[nexty][nextx] - 6][1].second;
								}
								else {
									y = wormhole[map[nexty][nextx] - 6][0].first;
									x = wormhole[map[nexty][nextx] - 6][0].second;
								}
								m = -1;
								continue;
							}
							// 블록을 만난 경우
							else if (map[y][x] >= 1 && map[y][x] <= 5) {
								dir = workBlock(dir, map[y][x]);
								curScore++;
								m = -1;
								continue;
							}
						}
					}
				}
		printf("#%d %d\n", t, ans);
	}

	return 0;
}
```
