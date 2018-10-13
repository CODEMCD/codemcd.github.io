---
title: "[SW Expert Academy] 5653.줄기세포배양"
excerpt: "시뮬레이션"
search: true
categories:
  - PS
tags:
  - SW Expert Academy
---

# 줄기세포배양

## 문제 링크
- [https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRJ8EKe48DFAUo&categoryId=AWXRJ8EKe48DFAUo&categoryType=CODE](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRJ8EKe48DFAUo&categoryId=AWXRJ8EKe48DFAUo&categoryType=CODE)

## 해결 방법
### 시뮬레이션
- 문제에서 세포 최대 크기가 주어지지 않았지만 초기 상태의 최대 크기가 가로/세로 50이며 최대 배양 시간이 300이므로 최대 크기는 가로/세로 350으로 예상할 수 있다.
- 세포 배양은 문제에서 주어진 대로 구현하며, 두 개 이상의 줄기 세포가 하나의 그리드 셀에 동시 번식하는 경우는 현재 시간에서 배양할 수 있는 모든 세포를 미리 번식시킨 뒤 생명력 수치를 기준으로 **내림차순** 정렬하여 수치가 가장 큰 세포가 번식하면 그 아래는 무시한다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
using namespace std;

//상방향부터 시계 방향
int my[4] = { -1, 0, 1, 0 };
int mx[4] = { 0, 1, 0, -1 };
//해당 좌표에 세포가 있는지 검사하는 2차원 배열
//문제에서 최대 한방향으로 350만큼 갈 수 있다.
bool visited[800][800];

//세포 정보
class cellInfo {
public:
	int y, x;      //세포 좌표
	int lifetime;  //생명력 수치
	int timeCnt;   //시간에 따른 생명력 수치 변화
	               //0 > timeCnt: 비활성 상태
	               //timeCnt == 0: 활성 상태 + 번식할 수 있는 상태
                   //0 > timeCnt > -lifetime: 활성 상태
	               //-lifetime >= timeCnt: 죽은 상태
};

//cv: 해당 시간에 존재하는 세포들의 정보를 저장한 벡터
//add: 해당 시간에 추가해야할 세포들의 정보를 저장한 벡터
vector<cellInfo> cv, add;

//생명력 수치를 내림차순으로 정렬한다.
bool comp(const cellInfo &a, const cellInfo &b) {
	return a.lifetime > b.lifetime;
}

int main(void)
{
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {
		//초기화
		memset(visited, false, sizeof(visited));
		cv.clear();

		int n, m, k; scanf("%d %d %d", &n, &m, &k);
		for (int i = 0; i < n; ++i)
			for (int j = 0; j < m; ++j) {
				int life; scanf("%d", &life);
				//생명력 수치가 0보다 큰 경우
				if (life > 0) {
					cv.push_back({ i, j, life, life });
					//좌표를 모두 양수로 만들기 위해 각각 400을 더한다.
					visited[i + 400][j + 400] = true;
				}
			}

		for (int i = 0; i < k; ++i) {
			add.clear();
			int cvSize = cv.size();

			//세포가 활성화 상태 + 번식 가능 상태일 때 번식할 세포의 정보를 임시 벡터에 저장한다.
			for (int j = 0; j < cvSize; ++j) {
				//상태 검사
				if (cv[j].timeCnt == 0) {
					for (int l = 0; l < 4; ++l) {
						int nexty = cv[j].y + my[l];
						int nextx = cv[j].x + mx[l];

						//이미 해당 좌표에 세포가 있으면 넘어간다.
						if (visited[nexty + 400][nextx + 400]) continue;
						//번식할 수 있는 모든 세포를 추가한다.(중복 좌표 포함)
						add.push_back({ nexty, nextx, cv[j].lifetime, cv[j].lifetime });
					}
				}
			}

			//번식한 세포를 제외한 모든 세포의 시간을 1 감소시킨다.
			for (int j = 0; j < cvSize; ++j)
				cv[j].timeCnt--;

			//번식한 세포들을 생명력 수치를 기준으로 내림차순으로 정렬한다.
			//해당 좌표에서 가장 큰 생명력 수치를 저장하고 그 이후는 무시하기 위함이다.
			sort(add.begin(), add.end(), comp);
			for (int j = 0; j < add.size(); ++j) {
				//해당 좌표에 세포가 없다면 세포를 추가한다.
				if (!visited[add[j].y + 400][add[j].x + 400]) {
					cv.push_back(add[j]);
					visited[add[j].y + 400][add[j].x + 400] = true;
				}
			}
		}

		int ans = 0;
		//활성 상태와 비활성 상태의 세포의 수를 센다.
		for (int i = 0; i < cv.size(); ++i)
			if (cv[i].timeCnt > -cv[i].lifetime)
				ans++;

		printf("#%d %d\n", t, ans);
	}

	return 0;
}
```
