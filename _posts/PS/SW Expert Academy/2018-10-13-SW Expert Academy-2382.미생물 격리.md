---
title: "[SW Expert Academy] 2382.미생물 격리"
excerpt: "시뮬레이션"
search: true
categories:
  - PS
tags:
  - SW Expert Academy
sidebar_main: true
---

# 미생물 격리

## 문제 링크
- [https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV597vbqAH0DFAVl](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV597vbqAH0DFAVl)

## 해결 방법
### 시뮬레이션
- 문제에서 제시된 시간에 따라 미생물의 움직임에 대한 경우의 수를 조건에 맞게 구현한다.
- 주의할 점은 임시로 사용할 배열을 반복문안에 선언하여 사용하면 시간이 훨씬 많이 걸린다.
  - 전역으로 선언하여 초기화해주면서 사용하는 것이 효율적이다.

## 시간 복잡도
- 최대 시간 복잡도는 ```1000(시간) * 1000(군집 개수) * 4(겹쳐질 수 있는 군집의 개수) = 4,000,000```이다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;

//상,하,좌,우
int my[4] = { -1, 1, 0, 0 };
int mx[4] = { 0, 0, -1, 1 };

//미생물 군집 정보 클래스
class state {
public:
	int y, x;
	int microNum;
	int dir;
};

//방향 반전
int reverseDir(int cur) {
	//상하 반전
	if (cur == 0)
		return 1;
	else if (cur == 1)
		return 0;
	//좌우 반전
	else if (cur == 2)
		return 3;
	else if (cur == 3)
		return 2;
	return -1;
}

//모든 미생물 군집 정보를 저장할 벡터
vector<state> mov;
//미생물 군집이 존재하는 좌표에 군집 인덱스를 저장할 2차원 벡터
vector<int> map[101][101];

int main(void)
{
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {
		mov.clear();
		int n, m, k; scanf("%d %d %d", &n, &m, &k);
		for (int i = 0; i < k; ++i) {
			int y, x, mn, d;
			scanf("%d %d %d %d", &y, &x, &mn, &d);
			mov.push_back({ y, x, mn, d - 1 });
		}
		int msize = mov.size();
		//시간 경과
		for (int i = 0; i < m; ++i) {
			//미생물 군집들이 이동한 결과를 갱신할 벡터
			vector<state> tmp;
			/*
			크기가 큰 저장공간을 계속해서 만들고 사용하는 것은 시간이 매우 오래걸린다.
			전역으로 선언 후 초기화하면서 사용하는 것이 시간 단축에 큰 도움이 된다.
			특히 이 문제에서는 map[y][x]을 한번 사용하면 바로 지워주기 때문에
			따로 초기화해줄 필요가 없어 전역으로 선언해서 사용하면 더욱 효율적이다.
			(map[y][x]를 중복해서 사용하는 것을 방지하기위해 사용하면 바로 지워준다.)
			*/
			//vector<int> map[101][101];

			//군집 이동
			for (int j = 0; j < msize; ++j) {
				int nexty = mov[j].y + my[mov[j].dir];
				int nextx = mov[j].x + mx[mov[j].dir];
				mov[j].y = nexty;
				mov[j].x = nextx;
				//이동한 좌표에 군집 인덱스 저장
				map[nexty][nextx].push_back(j);
			}

			//군집 갱신
			for (int j = 0; j < msize; ++j) {
				//해당 좌표의 군집 개수가 1개인 경우
				if (map[mov[j].y][mov[j].x].size() == 1) {
					//약품이 칠해진 셀에 도착한 경우
					if (mov[j].y == 0 || mov[j].y == n - 1
						|| mov[j].x == 0 || mov[j].x == n - 1) {
						state half;
						half.dir = reverseDir(mov[j].dir);
						half.y = mov[j].y;
						half.x = mov[j].x;
						half.microNum = mov[j].microNum / 2;
						//미생물 군집 크기가 0보다 큰 경우에만 갱신한다.
						if (half.microNum > 0)
							tmp.push_back(half);
						//해당 좌표의 인덱스를 지운다.
						map[mov[j].y][mov[j].x].clear();
					}
					//일반 셀인 경우
					else {
						tmp.push_back(mov[j]);
						map[mov[j].y][mov[j].x].clear();
					}
				}
				//해당 좌표의 군집 개수가 1보다 많은 경우
				else if (map[mov[j].y][mov[j].x].size() > 1) {
					int bigMicro = 0, bigIdx = 0, totalMicro = 0;
					//방향을 정할 가장 큰 군집 인덱스와 미생물의 총합을 구한다.
					for (int k = 0; k < map[mov[j].y][mov[j].x].size(); ++k) {
						if (bigMicro < mov[map[mov[j].y][mov[j].x][k]].microNum) {
							bigMicro = mov[map[mov[j].y][mov[j].x][k]].microNum;
							bigIdx = map[mov[j].y][mov[j].x][k];
						}
						totalMicro += mov[map[mov[j].y][mov[j].x][k]].microNum;
					}
					state summ;
					summ.dir = mov[bigIdx].dir;
					summ.y = mov[bigIdx].y;
					summ.x = mov[bigIdx].x;
					summ.microNum = totalMicro;
					tmp.push_back(summ);
					//해당 좌표에는 여러 군집이 저장되어 있으므로 지우지 않으면 중복이 발생한다.(궁극적인 이유)
					map[mov[j].y][mov[j].x].clear();
				}
			}

			//갱신한 임시 벡터를 원래 벡터에 저장해주고 크기를 다시 계산한다.
			mov = tmp;
			msize = mov.size();
		}

		//남은 미생물 군집을 모두 더한다.
		int ans = 0;
		for (int i = 0; i < msize; ++i)
			ans += mov[i].microNum;
		printf("#%d %d\n", t, ans);
	}

	return 0;
}
```
