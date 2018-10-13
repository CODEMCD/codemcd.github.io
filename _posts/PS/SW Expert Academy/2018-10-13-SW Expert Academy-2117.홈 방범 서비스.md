---
title: "[SW Expert Academy] 2117.홈 방범 서비스"
excerpt: "완전 탐색"
search: true
categories:
  - PS
tags:
  - SW Expert Academy
---

# 홈 방범 서비스

## 문제 링크
- [https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5V61LqAf8DFAWu](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AV5V61LqAf8DFAWu)

## 해결 방법
### 완전 탐색
- 서비스 영역의 면적이 최대 크기 ```20 x 20```을 모두 포함하려면 대략 k 값을 25로 정한다.
- k 값 1 ~ 25 까지 포함되는 모든 좌표를 벡터에 저장한다.
  - 원점 기준으로 하며, 이는 도시 정보의 한 칸 한 칸마다 서비스 영역을 적용하기 위해서이다.
- 도시 정보가 주어진 2차원 배열의 모든 좌표에서 위에서 저장한 서비스 영역을 적용하여 손해보지 않는 최대 집의 수를 구한다.

## 시간 복잡도
### 서비스 영역 구하기
- 다음 서비스 영역을 구하는 방법으로 현재 서비스 영역에서 동서남북 방향으로 한 칸씩 늘리는 방법을 사용하였다.(중복 제외)
- 서비스 영역의 개수는 ```1, 5, 13, 25, 41, 61, ... 1201```로 증가한다.
- 이를 바탕으로 최대 시간 복잡도는 대략```25 * 4(동서남북) * 1200 * 1200 = 144,000,000```으로 약 1초정도 걸릴 것으로 예상된다.

### 최대 집의 개수 구하기
- 최대 시간 복잡도는 ```25(서비스 영역 크기) * 20(가로) * 20(세로) * 1200(최대 서비스 영역 좌표 개수) = 12,000,000```이다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;

int my[4] = { -1, 0, 1, 0 };
int mx[4] = { 0, 1, 0, -1 };

//도시 입력
int cmap[21][21];
//kv[i] = k의 크기가 i일때 방범 서비스를 제공할 수 있는 모든 좌표의 집합
vector<pair<int, int>> kv[25];

//가로세로 최대 크기 20인 배열을 모두 방범 서비스를 제공할 k의 최대 크기는 대략 23 ~ 25이다.
//미리 k의 크기에 따른 방범 서비스를 할 수 있는 좌표 집합을 만든다.
void makeKSet() {
	//k = 1일때
	kv[0].push_back({ 0, 0 });
	//k = 2일때 부터 계산을 한다.
	for (int i = 1; i < 25; ++i) {
		int pre_ksize = kv[i - 1].size();
		kv[i] = kv[i - 1];
		for (int j = 0; j < pre_ksize; ++j) {
			for (int k = 0; k < 4; ++k) {
				int nexty = kv[i - 1][j].first + my[k];
				int nextx = kv[i - 1][j].second + mx[k];
				bool flag = false;
				//중복되는 좌표가 있는지 검사한다.
				for(int l = 0; l < kv[i].size(); ++l)
					if (kv[i][l].first == nexty && kv[i][l].second == nextx) {
						flag = true;
						break;
					}
				//중복이 없으면 추가한다.
				if (!flag)
					kv[i].push_back({ nexty, nextx });
			}
		}
	}
}

//k에 따른 운영 비용을 반환한다.
int calOperationFin(int k) {
	return (k * k) + ((k - 1) * (k - 1));
}

int main(void)
{
	makeKSet();
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {
		memset(cmap, 0, sizeof(cmap));
		int n, m; scanf("%d %d", &n, &m);
		for(int i=0; i<n; ++i)
			for(int j=0; j<n; ++j)
				scanf("%d", &cmap[i][j]);

		int ans = 0;
		//k에 따라 방범 서비스를 받을 수 있는 최대 집의 개수를 계산한다.
		for(int i = 0; i < 25; ++i) {
			//현재 k에서 운영 비용
			int curOF = calOperationFin(i + 1);
			//현재 k에서 모든 좌표를 순회한다.
			for (int y = 0; y < n; ++y) {
				for (int x = 0; x < n; ++x) {
					int serviced = 0, curProfit;
					//현재 좌표에서 방범 서비스를 받을 수 있는 집의 개수 계산
					for (int j = 0; j < kv[i].size(); ++j) {
						int nexty = y + kv[i][j].first;
						int nextx = x + kv[i][j].second;
						if (nexty >= 0 && nexty < n && nextx >= 0 && nextx < n)
							if (cmap[nexty][nextx])
								serviced++;
					}
					curProfit = (serviced * m) - curOF;
					//비용 손해를 보지 않는다면 최대 집 개수를 갱신한다.
					if (curProfit >= 0)
						ans = ans > serviced ? ans : serviced;		
				}
			}
		}

		printf("#%d %d\n", t, ans);
	}

	return 0;
}
```
