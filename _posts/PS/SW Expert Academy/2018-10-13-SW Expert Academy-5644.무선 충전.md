---
title: "[SW Expert Academy] 5644.무선 충전"
excerpt: "시뮬레이션"
search: true
sidebar_main: true
categories:
  - PS
tags:
  - SW Expert Academy
---

# 무선 충전

## 문제 링크
- [https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRDL1aeugDFAUo&categoryId=AWXRDL1aeugDFAUo&categoryType=CODE&&&](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRDL1aeugDFAUo&categoryId=AWXRDL1aeugDFAUo&categoryType=CODE&&&)

## 해결 방법
### 시뮬레이션
- 2차원 배열에 주어진 배터리 범위들을 저장한다.
- 두 사용자의 시작점부터 한 칸씩 이동할 때마다 배터리 범위에 포함되어 있는지 검사한다.
- 두 사용자가 배터리 범위에 있을 경우의 수는 총 3가지가 있다.
  - A사용자가 배터리 범위에 있고 B사용자는 배터리 범위에 있지 않을 경우
    - A사용자가 속한 배터리 범위 중 가장 큰 충전량을 선택한다.
  - A사용자가 배터리 범위에 없고 B사용자는 배터리 범위에 있는 경우
    - B사용자가 속한 배터리 범위 중 가장 큰 충전량을 선택한다.
  - A사용자, B사용자 둘 다 배터리 범위에 있는 경우
    - A, B사용자가 같은 배터리 범위에 있을 때의 충전량과 다른 배터리 범위에 있을 때 충전량 중 큰 값을 선택한다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;

//이동하지 않음, 상, 우, 하, 좌
int my[5] = { 0, -1, 0, 1, 0 };
int mx[5] = { 0, 0, 1, 0, -1 };

//배터리 정보
class APInfo {
public:
	int y, x;  //배터리 좌표
	int c;     //범위
	int p;     //충전량
};

//범위 크기에 따라 계산할 좌표 벡터(인덱스 0이 문제에서 범위 1를 나타낸다.)
vector<pair<int, int>> covSet[5];
//배터리 정보를 저장할 벡터
vector<APInfo> apv;
//사용자 A, B의 움직임을 저장할 벡터
vector<int> amove, bmove;
//지도에서 해당 좌표에 어떤 BC번호가 있는지 저장할 벡터(하나의 좌표에 여러 BC가 있을 수 있음)
vector<int> board[11][11];

//범위 크기에 따라 계산할 좌표 벡터를 만든다.
void makeCoverage() {
	//범위가 1인 경우
	covSet[0].push_back({ 0, 0 });
	covSet[0].push_back({ -1, 0 });
	covSet[0].push_back({ 0, 1 });
	covSet[0].push_back({ 1, 0 });
	covSet[0].push_back({ 0, -1 });
	//중복을 방지할 플래그
	bool flag;
	//문제 기준 범위 크기 5까지 만든다.
	//이전 범위에서 상하좌우를 각각 추가해준다.(중복 제외)
	for (int i = 1; i < 5; ++i) {
		covSet[i] = covSet[i - 1];
		//이전 범위에서 좌표 개수
		int preSize = covSet[i - 1].size();
		//현재 범위에서 좌표 개수
		int curSize = covSet[i].size();
		for (int j = 0; j < preSize; ++j) {
			//상하좌우 추가
			for (int k = 1; k < 5; ++k) {
				flag = false;
				int nexty = covSet[i][j].first + my[k];
				int nextx = covSet[i][j].second + mx[k];

				//현재 범위 좌표 벡터에 중복이 있는지 검사한다.
				for (int l = 0; l < curSize; ++l)
					if (covSet[i][l].first == nexty && covSet[i][l].second == nextx) {
						flag = true;
						break;
					}
				//중복이 없으면 추가한다.
				if (!flag) {
					covSet[i].push_back({ nexty, nextx });
					curSize++;
				}

			}
		}
	}
}

int main(void)
{
	makeCoverage();
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {

		//지도 초기화
		for (int i = 0; i < 11; ++i)
			for (int j = 0; j < 11; ++j)
				board[i][j].clear();

		int m, a; scanf("%d %d", &m, &a);
		//이동하지 않을 때도 충전을 검사해야하기 때문에 가장 처음에 이동하지 않는 경우를 추가한다.
		//입력 벡터 초기화
		amove = vector<int>(m + 1, 0);
		bmove = vector<int>(m + 1, 0);
		apv = vector<APInfo>(a);
		//이동하지 않은 경우
		amove[0] = 0;
		bmove[0] = 0;
		//시간에 따른 이동 정보 저장
		for (int i = 0; i < m; ++i)
			scanf("%d", &amove[i + 1]);
		for (int i = 0; i < m; ++i)
			scanf("%d", &bmove[i + 1]);
		//BC정보 입력
		for (int i = 0; i < a; ++i) {
			//입력은 x좌표부터인 것에 유의한다.
			scanf("%d %d %d %d", &apv[i].x, &apv[i].y, &apv[i].c, &apv[i].p);
			//문제에서는 (1, 1)부터 시작이므로 각각 1을 빼준다.
			apv[i].y -= 1;
			apv[i].x -= 1;
			//현재 범위 좌표 벡터의 크기
			int csize = covSet[apv[i].c - 1].size();
			//BC범위를 지도에 표시한다.
			for (int j = 0; j < csize; ++j) {
				int nexty = apv[i].y + covSet[apv[i].c - 1][j].first;
				int nextx = apv[i].x + covSet[apv[i].c - 1][j].second;
				//범위 내에 있는 경우
				if (nexty >= 0 && nexty < 10 && nextx >= 0 && nextx < 10)
					board[nexty][nextx].push_back(i);
			}
		}

		int ans = 0;
		//현재 사용자 A, B의 위치 좌표
		pair<int, int> cusA, cusB;
		//사용자 A의 처음 위치
		cusA = make_pair(0, 0);
		//사용자 B의 처음 위치
		cusB = make_pair(9, 9);
		//시뮬레이션(이동하지 않는 경우 포함)
		for (int i = 0; i <= m; ++i) {
			//사용자 A의 다음 위치
			cusA.first += my[amove[i]];
			cusA.second += mx[amove[i]];
			//사용자 B의 다음 위치
			cusB.first += my[bmove[i]];
			cusB.second += mx[bmove[i]];

			//현재 사용자A, B의 좌표에서 어떤 BC가 있는지 모두 저장할 벡터
			vector<int> covA, covB;
			//사용자 A 위치 좌표에서 BC 개수
			int asize = board[cusA.first][cusA.second].size();
			//사용자 B 위치 좌표에서 BC 개수
			int bsize = board[cusB.first][cusB.second].size();
			//BC 추가
			for (int j = 0; j < asize; ++j)
				covA.push_back(board[cusA.first][cusA.second][j]);
			for (int j = 0; j < bsize; ++j)
				covB.push_back(board[cusB.first][cusB.second][j]);

			int curChargeA = 0, curChargeB = 0, sumCharge = 0;
			//현재 좌표에서 최대 충전량을 구할 경우의 수는 총 3가지가 있다.
			// 1) 사용자 A 좌표에서 BC가 있고 사용자 B 좌표에서 BC가 없는 경우
			if (asize > 0 && bsize == 0) {
				for (int j = 0; j < asize; ++j)
					curChargeA = curChargeA > apv[covA[j]].p ? curChargeA : apv[covA[j]].p;
				sumCharge = curChargeA;
			}
			// 2) 사용자 A 좌표에서 BC가 없고 사용자 B 좌표에서 BC가 있는 경우
			else if (asize == 0 && bsize > 0) {
				for (int j = 0; j < bsize; ++j)
					curChargeB = curChargeB > apv[covB[j]].p ? curChargeB : apv[covB[j]].p;
				sumCharge = curChargeB;
			}
			// 3) 사용자 A, B 좌표에 BC가 모두 있는 경우
			else {
				for (int j = 0; j < asize; ++j) {
					for (int k = 0; k < bsize; ++k) {
						if (covA[j] == covB[k]) {
							curChargeA = apv[covA[j]].p / 2;
							curChargeB = apv[covB[k]].p / 2;
						}
						else {
							curChargeA = apv[covA[j]].p;
							curChargeB = apv[covB[k]].p;
						}
						int tmpSum = curChargeA + curChargeB;
						sumCharge = sumCharge > tmpSum ? sumCharge : tmpSum;
					}
				}
			}

			ans += sumCharge;
		}

		printf("#%d %d\n", t, ans);
	}

	return 0;
}
```
