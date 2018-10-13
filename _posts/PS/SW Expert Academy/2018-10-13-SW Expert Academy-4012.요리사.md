---
title: "[SW Expert Academy] 4012.요리사"
excerpt: "조합, 완전 탐색"
search: true
categories:
  - PS
tags:
  - SW Expert Academy
---

# 요리사

## 문제 링크
- [https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeUtVakTMDFAVH&categoryId=AWIeUtVakTMDFAVH&categoryType=CODE](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeUtVakTMDFAVH&categoryId=AWIeUtVakTMDFAVH&categoryType=CODE)

## 해결 방법
### 조합 + 완전탐색
- 재료를 2가지로 나누므로 모든 재료를 2가지로 나누는 모든 경우의 수를 조합으로 계산한다.
- 재료의 시너지는 각각 2개의 재료의 시너지를 모두 더한 값이다.

## 시간 복잡도
- 최대 식재료 개수: ```n = 16```
- 재료 시너지의 총합: ```(n/2) * (n/2) = 8 * 8 = 64```
- ```16C2 * 64 = 7,680```

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <string>
using namespace std;
const int INF = 987654321;
int mat[17][17];

int main(void)
{
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {
		memset(mat, 0, sizeof(mat));

		int n; scanf("%d", &n);
		for (int i = 0; i < n; ++i)
			for (int j = 0; j < n; ++j)
				scanf("%d", &mat[i][j]);
		int ans = INF;

		//전체 재료의 반을 선택하는 모든 경우의 수 계산(조합 사용)
		vector<int> flag;
		for (int i = 0; i < n - (n / 2); ++i)
			flag.push_back(0);
		for (int i = 0; i < n / 2; ++i)
			flag.push_back(1);
		do {
			int matA[17], matB[17];
			int cntA = 0, cntB = 0;
			//1: A재료, 0: B재료 로 사용
			for (int i = 0; i < n; ++i)
				if (flag[i])
					matA[cntA++] = i;
				else
					matB[cntB++] = i;

			//두가지 재료마다 시너지를 모두 더한다.
			int foodA = 0, foodB = 0;
			for (int i = 0; i < n / 2; ++i)
				for (int j =  i + 1; j < n / 2; ++j) {
					foodA += (mat[matA[i]][matA[j]] + mat[matA[j]][matA[i]]);
					foodB += (mat[matB[i]][matB[j]] + mat[matB[j]][matB[i]]);
				}
			//맛 차이 구하기
			int sub = foodA - foodB;
			sub = sub >= 0 ? sub : -sub;
			ans = ans < sub ? ans : sub;

			/*for (int i = 0; i < n; ++i)
				if (flag[i])
					printf("%d ", i);
			printf("\n");*/
		} while (next_permutation(flag.begin(), flag.end()));

		printf("#%d %d\n", t, ans);
	}

	return 0;
}
```
