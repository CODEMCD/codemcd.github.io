---
title: "[SW Expert Academy] 4013.특이한 자석"
excerpt: "구현"
search: true
categories:
  - PS
tags:
  - SW Expert Academy
sidebar_main: true
---

# 특이한 자석

## 문제 링크
- [https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeV9sKkcoDFAVH&categoryId=AWIeV9sKkcoDFAVH&categoryType=CODE](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeV9sKkcoDFAVH&categoryId=AWIeV9sKkcoDFAVH&categoryType=CODE)

## 해결 방법
### 구현
- 문제에서 제시한 톱니바퀴가 도는 조건을 바탕으로, 특정 톱니바퀴를 한 번 회전 시킬 때 같이 회전해야할 톱니바퀴들을 계산하여 그에 맞게 회전 시켜 준다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;

//자석을 회전하는 함수
//dir = 1: 시계 방향
//dir = -1: 반시계 방향
void rotate(int *gear, int dir) {
	if (dir == 1) {
		int tmp = gear[7];
		for (int i = 6; i >= 0; --i)
			gear[i + 1] = gear[i];
		gear[0] = tmp;
	}
	else if (dir == -1) {
		int tmp = gear[0];
		for (int i = 0; i < 7; ++i)
			gear[i] = gear[i + 1];
		gear[7] = tmp;
	}
}

int main(void)
{
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {
		//4개 자석
		int gear[4][8];
		int k; scanf("%d", &k);
		for (int i = 0; i < 4; ++i)
			for (int j = 0; j < 8; ++j)
				scanf("%d", &gear[i][j]);

		//회전
		for (int i = 0; i < k; ++i) {
			int n, d; scanf("%d %d", &n, &d);
			//하나의 톱니바퀴가 회전할 때, 각 자석에 적용해야할 회전 방향
			int rotRes[4];
			//1번 자석이 회전하는 경우
			if (n == 1) {
				rotRes[0] = d;
				if (gear[0][2] == gear[1][6]) {
					rotRes[1] = 0;
					rotRes[2] = 0;
					rotRes[3] = 0;
				}
				else {
					rotRes[1] = -d;
					if (gear[1][2] == gear[2][6]) {
						rotRes[2] = 0;
						rotRes[3] = 0;
					}
					else {
						rotRes[2] = d;
						if (gear[2][2] == gear[3][6])
							rotRes[3] = 0;
						else
							rotRes[3] = -d;
					}
				}
			}
			//2번 자석이 회전하는 경우
			else if (n == 2) {
				rotRes[1] = d;
				if (gear[1][2] == gear[2][6]) {
					rotRes[2] = 0;
					rotRes[3] = 0;
				}
				else {
					rotRes[2] = -d;
					if (gear[2][2] == gear[3][6])
						rotRes[3] = 0;
					else
						rotRes[3] = d;
				}
				if (gear[0][2] == gear[1][6])
					rotRes[0] = 0;
				else
					rotRes[0] = -d;
			}
			//3번 자석이 회전하는 경우
			else if (n == 3) {
				rotRes[2] = d;
				if (gear[2][2] == gear[3][6])
					rotRes[3] = 0;
				else
					rotRes[3] = -d;
				if (gear[1][2] == gear[2][6]) {
					rotRes[1] = 0;
					rotRes[0] = 0;
				}
				else {
					rotRes[1] = -d;
					if (gear[0][2] == gear[1][6])
						rotRes[0] = 0;
					else
						rotRes[0] = d;
				}
			}
			//4번 자석이 회전하는 경우
			else if (n == 4) {
				rotRes[3] = d;
				if (gear[2][2] == gear[3][6]) {
					rotRes[2] = 0;
					rotRes[1] = 0;
					rotRes[0] = 0;
				}
				else {
					rotRes[2] = -d;
					if (gear[1][2] == gear[2][6]) {
						rotRes[1] = 0;
						rotRes[0] = 0;
					}
					else {
						rotRes[1] = d;
						if (gear[0][2] == gear[1][6])
							rotRes[0] = 0;
						else
							rotRes[0] = -d;
					}
				}
			}
			//회전 적용 (0이면 회전하지 않음)
			for (int j = 0; j < 4; ++j)
				if (rotRes[j] != 0)
					rotate(gear[j], rotRes[j]);
		}
		//점수 계산
		printf("#%d %d\n", t, gear[0][0] * 1 + gear[1][0] * 2 +
			gear[2][0] * 4 + gear[3][0] * 8);
	}

	return 0;
}
```
