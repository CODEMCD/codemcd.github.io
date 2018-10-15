---
title: "[SW Expert Academy]5656.벽돌 깨기"
excerpt: "메모이제이션 + 완전 탐색"
search: true
categories:
  - PS
tags:
  - SW Expert Academy
---

# 벽돌 깨기

## 문제 링크
[https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRQm6qfL0DFAUo](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRQm6qfL0DFAUo)

## 해결 방법
### 메모이제이션
- 단순히 완전 탐색을 사용하였을 때는 시간 초과가 발생하였다.(중복 순열 사용)
- 최대 보드 크기가 ```12 X 15```로 작으므로 공을 3번 쏠 때까지 모든 경우의 보드 정보를 저장할 수 있다.
- 이를 활용하여 공을 한 번 쏠 때 모든 경우의 보드 내용을 저장하고, 두 번 쏠 때는 한 번 쐈을 때 보드 내용을 바탕으로 한 번만 더 계산하면 된다.

## 시간 복잡도
- 최대 보드 크기에서 블록 제거(BFS): ```12 * 15```
- 가로 크기 12에서 공을 4번 쏘는 모든 경우의 수(중복 순열): ```12^4```
- 단순 완전 탐색: ```12 * 15 * 12^4 * 4(공을 쏠 때마다 새로 계산하는 수, 공의 개수) = 14,929,920```
- 완전 탐색 + 메모이제이션 ```12 * 15 * 12^4 = 3,732,480```

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <queue>
using namespace std;
const int INF = 987654321;

class qState {
public:
	int y, x;
	int size;
};

int my[4] = { 1, 0, -1, 0 };
int mx[4] = { 0, 1, 0, -1 };

//보드 정보 저장
vector<vector<int>> board, tmp;
//메모이제이션
//1차원: 공을 한 번 쐈을 때의 모든 경우의 보드 정보를 저장
//2차원: 공을 두 번 쐈을 때의 모든 경우의 보드 정보를 저장
//3차원: 공을 세 번 쐈을 때의 모든 경우의 보드 정보를 저장
vector<vector<int>> cache[15][15][15];
//공을 1 ~ 3번 쐈을 때마다 부서진 벽돌의 개수 저장
int bbn[15][15][15];
vector<int> set;
queue<qState> q;
int n, w, h;

//해당 좌표에 공을 쐈을 때 변화한 보드 내용을 계산하고 부서진 벽돌 개수를 반환한다.
int shotAndDrop(int y, int x) {
	//벽돌 부수기(BFS 활용)
	int brokenBlock = 0;
	q.push({ y, x, tmp[y][x] });
	brokenBlock++;
	tmp[y][x] = 0;
	while (!q.empty()) {
		int nowy = q.front().y;
		int nowx = q.front().x;
		int size = q.front().size;
		q.pop();

		for (int i = 0; i < 4; ++i) {
			int nexty = nowy, nextx = nowx;
			for (int k = 0; k < size - 1; ++k) {
				nexty += my[i];
				nextx += mx[i];
				if (nexty >= 0 && nexty < h && nextx >= 0 && nextx < w)
					if (tmp[nexty][nextx] > 0) {
						if (tmp[nexty][nextx] > 1)
							q.push({ nexty, nextx, tmp[nexty][nextx] });
						tmp[nexty][nextx] = 0;
						brokenBlock++;
					}
			}
		}
	}

	//중간중간 비어진 공간 채우기
	for (int i = 0; i < w; ++i) {
		set.clear();
		for (int k = h - 1; k >= 0; --k)
			if (tmp[k][i] > 0) {
				set.push_back(tmp[k][i]);
				tmp[k][i] = 0;
			}
		int idx = h - 1;
		for (int k = 0; k < set.size(); ++k)
			tmp[idx--][i] = set[k];
	}

	return brokenBlock;
}

int main(void)
{
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {
		board.clear();
		memset(bbn, 0, sizeof(bbn));
		scanf("%d %d %d", &n, &w, &h);
		board = vector<vector<int>>(h, vector<int>(w));
		int totalBlock = 0;
		for (int i = 0; i < h; ++i)
			for (int j = 0; j < w; ++j) {
				scanf("%d", &board[i][j]);
				if (board[i][j] > 0)
					totalBlock++;
			}

		int ans = INF;
		for (;;) {
			//공을 한 번 쏜 모든 경우
			for (int i = 0; i < w; ++i) {
				tmp = board;
				int brokenIdx = -1;
				for (int k = 0; k < h; ++k)
					if (tmp[k][i] > 0) {
						brokenIdx = k;
						break;
					}

				//해당 좌표에 부술 벽돌이 없으면 보드 정보를 그대로 저장한다.
				if (brokenIdx == -1) {
					cache[i + 1][0][0] = tmp;
					continue;
				}

				//해당 좌표에 공을 쐈을 때 부서진 벽돌 개수를 저장한다.
				int brokenBlock = shotAndDrop(brokenIdx, i);

				//변화한 정보를 각각 저장(메모이제이션)
				bbn[i + 1][0][0] = brokenBlock;
				cache[i + 1][0][0] = tmp;
				//남은 벽돌 개수 계산
				int curBlock = totalBlock - bbn[i + 1][0][0];
				ans = ans < curBlock ? ans : curBlock;
			}
			if (n == 1) break;

			//공을 두 번 쏜 모든 경우
			for (int i = 0; i < w; ++i) {
				for (int j = 0; j < w; ++j) {
					//해당 좌표에 공을 한 번 쏜 경우의 보드 내용을 사용한다.
					tmp = cache[i + 1][0][0];
					int brokenIdx = -1;
					for (int k = 0; k < h; ++k)
						if (tmp[k][j] > 0) {
							brokenIdx = k;
							break;
						}

					if (brokenIdx == -1) {
						cache[i + 1][j + 1][0] = tmp;
						continue;
					}

					int brokenBlock = shotAndDrop(brokenIdx, j);

					bbn[i + 1][j + 1][0] = bbn[i + 1][0][0] + brokenBlock;
					cache[i + 1][j + 1][0] = tmp;
					int curBlock = totalBlock - bbn[i + 1][j + 1][0];
					ans = ans < curBlock ? ans : curBlock;
				}
			}
			if (n == 2) break;

			//공을 세 번 쏜 모든 경우
			for (int l = 0; l < w; ++l) {
				for (int i = 0; i < w; ++i) {
					for (int j = 0; j < w; ++j) {
						tmp = cache[l + 1][i + 1][0];
						int brokenIdx = -1;
						for (int k = 0; k < h; ++k)
							if (tmp[k][j] > 0) {
								brokenIdx = k;
								break;
							}

						if (brokenIdx == -1) {
							cache[l + 1][i + 1][j + 1] = tmp;
							continue;
						}

						int brokenBlock = shotAndDrop(brokenIdx, j);

						bbn[l + 1][i + 1][j + 1] = bbn[l + 1][i + 1][0] + brokenBlock;
						cache[l + 1][i + 1][j + 1] = tmp;
						int curBlock = totalBlock - bbn[l + 1][i + 1][j + 1];
						ans = ans < curBlock ? ans : curBlock;
					}
				}
			}
			if (n == 3) break;

			//공을 네 번 쏜 모든 경우
			for (int i = 0; i < w; ++i) {
				for (int l = 0; l < w; ++l) {
					for (int kk = 0; kk < w; ++kk) {
						for (int j = 0; j < w; ++j) {
							tmp = cache[i + 1][l + 1][kk + 1];
							int brokenIdx = -1;
							for (int k = 0; k < h; ++k)
								if (tmp[k][j] > 0) {
									brokenIdx = k;
									break;
								}

							if (brokenIdx == -1) continue;

							int brokenBlock = shotAndDrop(brokenIdx, j);

							int curBlock = totalBlock - (bbn[i + 1][l + 1][kk + 1] + brokenBlock);
							ans = ans < curBlock ? ans : curBlock;
						}
					}
				}
			}
			if (n == 4) break;
		}

		printf("#%d %d\n", t, ans);
	}

	return 0;
}
```
