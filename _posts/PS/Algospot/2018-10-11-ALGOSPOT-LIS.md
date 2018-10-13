---
title: "[ALGOSPOT]LIS"
excerpt: "DP"
search: true
categories:
  - PS
tags:
  - Algospot
---

# LIS(Longest Increasing Sequence)

## 문제 링크
- [https://algospot.com/judge/problem/read/LIS](https://algospot.com/judge/problem/read/LIS)

## 코드 분석
### Bottom-up
- DP[N]: 현재 인덱스에서 가장 긴 증가 부분 수열의 길이를 저장한다.
- Input[N]: 입력 값을 저장한다.
- 현재 인덱스가 i일 때, j: (1 ~ i - 1)중 만약 ```input[i] < input[j]```이라면 DP[i] = DP[j] + 1로 갱신한다.
- 갱신된 값 중 가장 큰 값을 해당 인덱스의 최적해로 판단한다.
- 시간 복잡도: ```1 + 2 + 3 + ... + n```이므로, O(n^2)이다.

### Top-down
- chache[N]: 현재 인덱스에서 가장 긴 증가 부분 수열의 길이를 저장한다.(메모이제이션)
- S[N]: 입력 배열
- lis(start) = S[start]에서 시작하는 부분 증가 수열 중 최대의 길이를 반환한다.
- 별도의 기저 사례 없이, for문의 조건문을 이용한다.
- 시간 복잡도: 총 n개의 부분 문제를 갖고, 하나를 해결할 때마다 n번 연산하므로 O(n^2)이다.

## 결과 코드
- Bottom-up

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;
#define N 501
int dp[N];
int input[N];

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		memset(dp, 0, sizeof(dp));
		memset(input, 0, sizeof(input));

		int n;
		scanf("%d", &n);
		for (int i = 0; i < n; i++)
			scanf("%d", &input[i]);

		//dp배열을 모두 1로 초기화(숫자가 한개 이면 길이가 1이기 때문)
		for (int i = 0; i < n; i++)
			dp[i] = 1;
		//i: 현재 인덱스, j: 이전 인덱스
		//dp배열에서 이전의 인덱스들의 값들은 최적해이다.
		//입력 배열에서 현재 인덱스의 값이 이전의 인덱스의 값보다 크다면
		//해당 dp배열의 값에서 1을 더하여 갱신하며, 갱신 값중의 최대값을 최적해라고 판단한다.
		for (int i = 1; i < n; i++) {
			for (int j = 0; j < i; j++) {
				if (input[j] < input[i]) {
					if (dp[i] < (dp[j] + 1))
						dp[i] = dp[j] + 1;
				}
			}
		}
		int max = 0;
		for (int i = 0; i < n; i++)
			if (max < dp[i])
				max = dp[i];

		printf("%d\n", max);
	}

	return 0;
}
```

- Top-down
```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;
#define max(a, b) ((a)>(b)?(a):(b))
#define N 501
int n;
int cache[N], S[N];

int lis(int start)
{
	int &ret = cache[start];
	if (ret != -1) return ret;
	ret = 1;
	for (int next = start + 1; next < n; ++next)
		if (S[start] < S[next])
			ret = max(ret, lis(next) + 1);
	return ret;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		memset(cache, -1, sizeof(cache));
		memset(S, 0, sizeof(S));

		scanf("%d", &n);
		for (int i = 0; i < n; i++)
			scanf("%d", &S[i]);
		int max = 0;
		for (int i = 0; i < n; i++) {
			int res = lis(i);
			if (max < res)
				max = res;
		}
		printf("%d\n", max);
	}

	return 0;
}
```
