---
title: "[SW Expert Academy] 4008.숫자 만들기"
excerpt: "순열, 완전 탐색"
search: true
categories:
  - PS
tags:
  - SW Expert Academy
---

# 숫자 만들기

## 문제 링크
- [https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeRZV6kBUDFAVH&categoryId=AWIeRZV6kBUDFAVH&categoryType=CODE](https://www.swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWIeRZV6kBUDFAVH&categoryId=AWIeRZV6kBUDFAVH&categoryType=CODE)

## 해결 방법
### 순열 + 완전탐색
- 주어진 연산자들이 정해진 숫자 사이의 공간에 위치하는 모든 경우의 수를 순열로 계산하여 해결한다.
  - 연산자의 종류는 4개이므로 4개보다 많은 연산자를 사용하면 반드시 중복이 생긴다.
  - 중복이 m개 있는 수열 n에서 r개를 뽑는 순열은 ```nPr / m!```이다.

## 시간 복잡도
- 최대 연산자 개수(n): ```11```
- 최대 주어진 공간(r): ```11```
- ```11P11(= 11!) / m!```

## Notice
- 음수가 나올 수 있으므로, 가장 작은 값을 설정할 때 0이 아닌 최대 음수값으로 설정해야 한다.

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
const int RINF = -987654321;

int main(void)
{
	int tc; scanf("%d", &tc);
	for (int t = 1; t <= tc; ++t) {
		int n; scanf("%d", &n);
		// 0: +, 1: -, 2: *, 3: /
		int oper[4] = { 0, };
		vector<int> opv;
		for (int i = 0; i < 4; ++i) {
			scanf("%d", &oper[i]);
			//연산자별로 저장하기(조합 탐색을 위해)
			for (int j = 0; j < oper[i]; ++j)
				opv.push_back(i);
		}
		int num[13] = { 0, };
		for (int i = 0; i < n; ++i)
			scanf("%d", &num[i]);

		//최대값은 음수를 포함한 범위안에서 가장 작은수
		//최소값은 가장 큰수로 초기화한다.
		int ans_max = RINF, ans_min = INF;
		do {
			int cal_res = num[0];
			int cnt = 0;
			for (int i = 1; i < n; ++i) {
				switch (opv[cnt++]) {
				case 0: cal_res += num[i]; break;
				case 1: cal_res -= num[i]; break;
				case 2: cal_res *= num[i]; break;
				case 3: cal_res /= num[i]; break;
				default: break;
				}
			}
			ans_max = ans_max > cal_res ? ans_max : cal_res;
			ans_min = ans_min < cal_res ? ans_min : cal_res;

			/* //연산자의 모든 경우의 수 출력해보기
			for (int i = 1; i < n; ++i) {
				switch (opv[cnt++]) {
				case 0: printf("+ "); break;
				case 1: printf("- "); break;
				case 2: printf("* "); break;
				case 3: printf("/ "); break;
				default: break;
				}
			}
			printf("\n");*/
		} while (next_permutation(opv.begin(), opv.end()));
		printf("#%d %d\n", t, ans_max - ans_min);
	}

	return 0;
}
```
