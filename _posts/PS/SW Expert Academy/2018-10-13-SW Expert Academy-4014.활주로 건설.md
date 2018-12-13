---
title: "[SW Expert Academy] 4014.활주로 건설"
excerpt: "구현"
search: true
sidebar_main: true
categories:
  - Algorithm
tags:
  - Algorithm_FCT
---

# 활주로 건설

## 문제 링크
- [https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeW7FakkUDFAVH&categoryId=AWIeW7FakkUDFAVH&categoryType=CODE](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeW7FakkUDFAVH&categoryId=AWIeW7FakkUDFAVH&categoryType=CODE)

## 해결 방법
### 구현
- 활주로를 만드는 경우의 수를 총 4가지로 구분하였다.
  - 경사가 모두 같은 경우
  - 경사 차이가 1보다 큰 경우(활주로를 지을 수 없음)
  - 오르막 설치
  - 내리막 설치
> 구현 능력에 따라 코드 길이를 대폭 줄일 수 있는 것 같다. 나는 실력이 부족해서 안되는 경우가 있으면 다시 추가 추가하면서 구현하였다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>
using namespace std;
#define N 21
//입력 배열
int map[N][N];

int main(void)
{
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {
		memset(map, 0, sizeof(map));

		int n, x; scanf("%d %d", &n, &x);
		for (int i = 0; i < n; ++i)
			for (int j = 0; j < n; ++j)
				scanf("%d", &map[i][j]);

		//활주로 개수
		int ans = 0;
		//활주로를 건설할 수 있는지 검사
		bool buildPos = false;
		//가로 방향
		for (int i = 0; i < n; ++i) {
			//한 활주로에서 경사로를 지을수 있는지 없는지 검사하는 배열
			bool check[N] = { false, };
			buildPos = false;
			//높이가 같은 인덱스를 한 그룹으로 묶는다.
			//preIdx: 이전 그룹의 가장 마지막 인덱스(0으로 초기화)
			//curIdx: 현재 그룹의 가장 마지막 인덱스
			int preIdx = 0;
			for (int j = 0; j < n; ++j) {
				//높이 같은 인덱스 개수
				int sameCnt = 1;
				int curIdx;
				//높이가 같은 인덱스 개수 계산
				for (curIdx = j + 1; curIdx < n; ++curIdx) {
					if (map[i][j] == map[i][curIdx])
						sameCnt++;
					else
						break;
				}
				//현재 인덱스를 현재 그룹 가장 마지막 인덱스에 맞춘다.
				curIdx -= 1;
				//주어진 x길이보다 크거나 같다면 해당 그룹을 모두 true값을 넣어준다.
				if (sameCnt >= x) {
					//preIdx를 0으로 초기화 하였기 때문에 처음 그룹이 인덱스 0을 포함한다면 0도 true값을 넣어준다.
					if (!preIdx) check[0] = true;
					for (int k = preIdx + 1; k <= curIdx; ++k)
						check[k] = true;
				}

				// 1) preIdx가 0이고, preIdx와 curIdx의 높이가 같다면 이 그룹은 같은 그룹이며 모두 높이가 같다.
				if (preIdx == 0 && map[i][preIdx] - map[i][curIdx] == 0)
					buildPos = true;
				// 2) 이전 그룹보다 현재 그룹의 높이가 1 작은 경우,
				// 현재 그룹의 처음부터 x개의 경사로를 만들 수 있어야 한다.
				else if (map[i][preIdx] - map[i][curIdx] == 1 && (preIdx + x) < n
					&& check[preIdx + x]) {
					buildPos = true;
					//x개의 경사로를 만들었으므로, 해당 인덱스에서 중복으로 경사로를 지을 수 없다.
					for (int k = preIdx + 1; k <= preIdx + x; ++k)
						check[k] = false;
				}
				// 3) 이전 그룹보다 현재 그룹의 높이가 1 큰 경우,
				// 이전 그룹의 마지막부터 x개의 경사로를 만들 수 있어야 한다.
				else if (map[i][preIdx] - map[i][curIdx] == -1 && (preIdx - x + 1) >= 0
					&& check[preIdx - x + 1]) {
					buildPos = true;
					for (int k = preIdx - x + 1; k <= preIdx; ++k)
						check[k] = false;
				}
				// 4) 높이차이가 1보다 크거나, 경사로를 지을 수 없는 경우
				else {
					buildPos = false;
					break;
				}
				//현재 인덱스를 이전 인덱스에 삽입한다.
				preIdx = curIdx;
				j = curIdx;
			}
			//활주로를 지을 수 있다면 개수를 증가한다.
			if (buildPos) ans++;
		}

		//세로 방향
		//가로 방향 알고리즘에서 map배열의 인덱스를 서로 바꿔준다.
		for (int i = 0; i < n; ++i) {
			bool check[N] = { false, };
			buildPos = false;
			int preIdx = 0;
			for (int j = 0; j < n; ++j) {
				int sameCnt = 1;
				int curIdx;
				for (curIdx = j + 1; curIdx < n; ++curIdx) {
					if (map[j][i] == map[curIdx][i])
						sameCnt++;
					else
						break;
				}
				curIdx -= 1;
				if (sameCnt >= x) {
					if (!preIdx) check[0] = true;
					for (int k = preIdx + 1; k <= curIdx; ++k)
						check[k] = true;
				}

				if (preIdx == 0 && map[preIdx][i] - map[curIdx][i] == 0)
					buildPos = true;
				else if (map[preIdx][i] - map[curIdx][i] == 1 && (preIdx + x) < n
					&& check[preIdx + x]) {
					buildPos = true;
					for (int k = preIdx + 1; k <= preIdx + x; ++k)
						check[k] = false;
				}
				else if (map[preIdx][i] - map[curIdx][i] == -1 && (preIdx - x + 1) >= 0
					&& check[preIdx - x + 1]) {
					buildPos = true;
					for (int k = preIdx - x + 1; k <= preIdx; ++k)
						check[k] = false;
				}
				else {
					buildPos = false;
					break;
				}
				preIdx = curIdx;
				j = curIdx;
			}
			if (buildPos) ans++;
		}

		printf("#%d %d\n", t, ans);
	}

	return 0;
}
```
