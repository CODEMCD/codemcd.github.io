---
title: "[SW Expert Academy] 5648.원자 소멸 시뮬레이션"
excerpt: "구현"
search: true
sidebar_main: true
categories:
  - PS
tags:
  - SW Expert Academy
---

# 원자 소멸 시뮬레이션

## 문제 링크
-[https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRFInKex8DFAUo](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXRFInKex8DFAUo)

## 해결 방법
### 시뮬레이션
- 주어진 원자들을 시간마다 직접 옮기면서 서로 부딪혔는지 검사한다.
  - 한 칸을 두고 부딪힌 경우 0.5초가 소요되기 때문에 시간 단위는 0.5초씩 증가한다.
### 구현
- 위의 시뮬레이션으로 모든 탐색을 하면 시간 초과가 발생한다. 그러므로 좌표를 이용하여 직접 원자를 옮기지 않고 각 원자들을 서로 비교하며 충돌할 수 있는지 검사해야 한다.
- 먼저, 각 원자마다 충돌할 수 있는 모든 원자들을 검사하여 그 정보를 저장한다.
  - 저장하는 정보는 서로 충돌하는 두 원소의 번호, 충돌 시간이다.
  - 충돌하는 조건은 서로 충돌하는 좌표 4개를 임시로 만들어 계산하여 도출하였다.
    - **위** 방향을 예로 들면, 같은 선상에 있는 **아래** 방향은 같은 x좌표에 있고, **위** y 좌표가 **아래** y 좌표보다 작아야 서로 충돌할 수 있다. **좌**, **우** 방향은 **위** 방향과의 x, y 좌표 차이와 부호를 바탕으로 조건을 만든다.
- 모든 충돌이 저장된 벡터를 충돌 시간을 기준으로 오름 차순으로 정렬한다.
  - 그 이유는 각 원자마다 충돌할 수 있는 경우는 여러가지이지만 가장 빠른 시간에 충돌하는 원자들은 소멸하므로 그 이후를 무시하기 위해서이다.
- bool 타입의 check 배열로 처음 충돌한 원소들을 모두 검사한다.(false - 아직 해당 원소는 충돌하지 않음)
  - 한 충돌에서 두 원자가 모두 false인 경우는 처음 충돌하는 경우이므로 두 원자는 충돌 가능하다.
  - 한 원자만 false인 경우, 이미 충돌한 다른 원자의 충돌 시간과 현재 충돌 시간이 같다면 이 충돌하지 않은 원자는 이미 충돌한 원자와 충돌해야한다는 것을 알 수 있다.(단적인 예로는 3개의 원자가 충돌하는 것을 검사하기 위함이다.)

## 시간 복잡도
### 시뮬레이션
- 시간 복잡도를 줄이기 위해 부딪혔는지 검사하는 부분을 x좌표 기준으로 정렬하여 원자 배열을 한번만 탐색해도 모두 검사할 수 있도록 구현하였다.
- 원자의 위치 범위는 ```-1,000 ~ 1,000```이고, 0.5씩 증가하므로 원자가 이동할 수 있는 최대 횟수는 ```4,000```회이다.
- ```4,000 * (1,000(원자 이동) + 1,000*log1,000(원자 정렬) + 1000(충돌 검사)) = 20,000,000```

> 원자가 움직일 수 있는 최대 횟수인 4,000번을 돌리면 시간 초과가 뜨는데 3,500으로 하면 통과를 한다. 아마 내부 케이스가 부족해서 그런 것 같다.

### 구현
- 최대 원자 개수 ```N = 1,000```
- 충돌 검사: ```N * N```
- 충돌 벡터 오름 차순 정렬: ```NlogN```
- 충돌 검사: ```N```
- ```O(N^2)```

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

class collision {
public:
	int a1, a2;  //충돌한 두 원자
	float time;  //충돌 시간
};
vector<collision> cv;

//배열 인덱스 = 원자 번호
int atom[1001][4];   //x좌표, y좌표, 방향, 에너지
float atomCT[1001];  //각 원자의 충돌 시간
bool check[1001];    //충돌했는지 여부

bool comp(const collision &a, const collision &b) {
	return a.time < b.time;
}

//각 원자마다 모든 충돌을 저장한다.
void checkCollision(int n) {
	for (int i = 0; i < n; ++i) {
		for (int j = 0; j < n; ++j) {
			//두 원소의 x좌표 차이
			int dx = atom[i][0] - atom[j][0];
			//두 원소의 y좌표 차이
			int dy = atom[i][1] - atom[j][1];
			//현재 원소 방향이 '상'인 경우
			if (i != j && atom[i][2] == 0) {
				//비교하는 원자의 방향이 '하'이고, x좌표가 같고,
				//아래 방향의 원소 y좌표가 더 위에 있는 경우 추가한다.
				if (atom[j][2] == 1 && atom[i][0] == atom[j][0] &&
					atom[i][1] < atom[j][1])
					cv.push_back({ i, j, ((atom[j][1] - atom[i][1]) / (float)2) });
				//비교하는 원자의 방향이 '우'이고, x좌표 차이가 0보다 크고,
				//y좌표 차이가 0보다 작고, 두 차이의 크기가 같은 경우 추가한다.
				else if (atom[j][2] == 3 && dx > 0 && dy < 0 && dx == -dy)
					cv.push_back({ i, j, (float)dx });
				//비교하는 원자의 방향이 '좌'이고, x좌표 차이가 0보다 작고,
				//y좌표 차이가 0보다 작고, 두 차이의 크기가 같은 경우 추가한다.
				else if (atom[j][2] == 2 && dx < 0 && dy < 0 && dx == dy)
					cv.push_back({ i, j, (float)-dx });
			}
			//현재 원소 방향이 '하'인 경우
			else if (i != j && atom[i][2] == 1) {
				if (atom[j][2] == 0 && atom[i][0] == atom[j][0] &&
					atom[i][1] > atom[j][1])
					cv.push_back({ i, j, ((atom[i][1] - atom[j][1]) / (float)2) });
				else if (atom[j][2] == 3 && dx > 0 && dy > 0 && dx == dy)
					cv.push_back({ i, j, (float)dx });
				else if (atom[j][2] == 2 && dx < 0 && dy > 0 && dx == -dy)
					cv.push_back({ i, j, (float)-dx });
			}
			//현재 원소 방향이 '좌'인 경우
			else if (i != j && atom[i][2] == 2) {
				if (atom[j][2] == 3 && atom[i][1] == atom[j][1] &&
					atom[i][0] > atom[j][0])
					cv.push_back({ i, j, ((atom[i][0] - atom[j][0]) / (float)2) });
				else if (atom[j][2] == 0 && dx > 0 && dy > 0 && dx == dy)
					cv.push_back({ i, j, (float)dx });
				else if (atom[j][2] == 1 && dx > 0 && dy < 0 && dx == -dy)
					cv.push_back({ i, j, (float)dx });
			}
			//현재 원소 방향이 '우'인 경우
			else if (i != j && atom[i][2] == 3) {
				if (atom[j][2] == 2 && atom[i][1] == atom[j][1] &&
					atom[i][0] < atom[j][0])
					cv.push_back({ i, j, ((atom[j][0] - atom[i][0]) / (float)2) });
				else if (atom[j][2] == 0 && dx < 0 && dy > 0 && dx == -dy)
					cv.push_back({ i, j, (float)-dx });
				else if (atom[j][2] == 1 && dx < 0 && dy < 0 && dx == dy)
					cv.push_back({ i, j, (float)-dx });
			}
		}
	}
}

void init() {
	for (int i = 0; i < 1001; ++i)
		check[i] = false;
	cv.clear();
}

int main(void)
{
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {
		init();

		int n; scanf("%d", &n);
		for (int i = 0; i < n; ++i)
			scanf("%d %d %d %d", &atom[i][0], &atom[i][1],
				&atom[i][2], &atom[i][3]);

		//충돌 검사
		checkCollision(n);

		//충돌 시간을 기준으로 오름차순 정렬(후의 충돌들을 무시하기 위함)
		sort(cv.begin(), cv.end(), comp);
		int ans = 0;
		int csize = cv.size();
		for (int i = 0; i < csize; ++i) {
			//두 원소가 처음 충돌인 경우
			if (!check[cv[i].a1] && !check[cv[i].a2]) {
				ans += atom[cv[i].a1][3];
				ans += atom[cv[i].a2][3];
				check[cv[i].a1] = true;
				check[cv[i].a2] = true;
				atomCT[cv[i].a1] = cv[i].time;
				atomCT[cv[i].a2] = cv[i].time;
			}
			//두 원소 중 한 원소가 충돌하지 않은 경우에 그 원소가 이미 충돌한 원소와 충돌 시간이 같다면
			//이 원소는 같은 시간에 충돌한 것이다.(원소 3개가 충돌한 경우)
			else if (!check[cv[i].a1] && check[cv[i].a2] && atomCT[cv[i].a2] == cv[i].time) {
				ans += atom[cv[i].a1][3];
				check[cv[i].a1] = true;
				atomCT[cv[i].a1] = cv[i].time;
			}
			else if (check[cv[i].a1] && !check[cv[i].a2] && atomCT[cv[i].a1] == cv[i].time) {
				ans += atom[cv[i].a2][3];
				check[cv[i].a2] = true;
				atomCT[cv[i].a2] = cv[i].time;
			}
		}
		printf("#%d %d\n", t, ans);
	}

	return 0;
}
```
