---
title: "[ALGOSPOT]TRIANGLEPATH"
excerpt: "DP"
search: true
sidebar_main: true
categories:
  - PS
tags:
  - Algospot
---

# TRIANGLEPATH

## 문제 링크
- [https://algospot.com/judge/problem/read/TRIANGLEPATH](https://algospot.com/judge/problem/read/TRIANGLEPATH)

## 코드 분석
### Bottom-up
- DP[y][x] = (y,x)일 때, 최대합
- DP[0][0] = triangle[0][0]
- DP[y][x] = triangle[y][x] + max(DP[y-1][x], DP[y-1][x-1])
- 시간복잡도: O(n^2), 부분 문제 수 n^2 * 부분 문제 계산 2

### Top-down + 메모이제이션
- y, x는 재귀 호출이 풀어야 할 부분 문제를 지정한다. 즉, 이들은 앞으로 풀어야 할 조각들에 대한 정볼르 주는 입력들이다.
- path(y,x)는 (y,x)에서 시작해서 맨 아래줄까지 내려가는 부분 경로의 최대합을 반환한다.
- 점화식: path(y,x) = trinangle[y][x] + max(path(y+1,x), path(y+1, x+1))
- 시간복잡도: O(n^2), 부분 문제수 n^2 * 부분 문제 계산 시간 M(상수)

### ___최적 부분 구조___
- 동적 계획법의 중요한 요소로서, 어떤 문제와 분할 방식에 성립하는 조건이다.
- 각 부분 문제의 최적해만 있으면 전체 문제의 최적해를 쉽게 얻어낼 수 있는 경우이다.
- 대두분의 최적 부분 구조는 직관적으로 이해할 수 있지만, 그렇지 않은 경우는 대게 귀류법이나 대우를 이용해 증명할 수 있다.

## 결과 코드
- Bottom-up

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;
#define N 101
int dp[N][N];
int tri[N][N];
int mj[2] = { -1, 0 };
int mi[2] = { -1, -1 };

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		memset(dp, 0, sizeof(dp));
		memset(tri, 0, sizeof(dp));
		int n;
		scanf("%d", &n);
		for (int i = 0; i < n; i++) {
			for (int j = 0; j < n; j++) {
				if (j > i)
					break;
				scanf("%d", &tri[i][j]);
			}
		}
		//윗 줄부터 차례대로 내려가면서 최대합을 계산한다.
		dp[0][0] = tri[0][0];
		for (int i = 1; i < n; i++) {
			for (int j = 0; j < n; j++) {
				if (j > i)
					break;
				for (int f = 0; f < 2; f++) {
					int fi = i + mi[f];
					int fj = j + mj[f];
					if (fj >= 0 && fj < i)
						if (dp[i][j] < (tri[i][j] + dp[fi][fj]))
							dp[i][j] = tri[i][j] + dp[fi][fj];
				}
			}
		}
		/*for (int i = 0; i < n; i++) {
		for (int j = 0; j < n; j++)
		printf("%d ", dp[i][j]);
		printf("\n");
		}*/
		int max = 0;
		for (int i = 0; i < n; i++) {
			if (max < dp[n - 1][i])
				max = dp[n - 1][i];
		}
		printf("%d\n", max);
	}

	return 0;
}
```

- Top-down + 메모이제이션

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
using namespace std;
#define N 100
#define max(a,b) ((a)>(b)?(a):(b))
int n, triangle[N][N];
int cache[N][N];

//(y, x) 위치부터 맨 아래줄까지 내려가면서 얻을 수 있는 최대 경로의 합을 반환한다.
int path(int y, int x)
{
	//기저 사례: 맨 아래 줄까지 도달했을 경우
	if (y == n - 1) return triangle[y][x];
	//메모이제이션
	int & ret = cache[y][x];
	if (ret != -1) return ret;
	return ret = max(path(y + 1, x), path(y + 1, x + 1))
		+ triangle[y][x];
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		memset(triangle, 0, sizeof(triangle));
		memset(cache, -1, sizeof(cache));
		scanf("%d", &n);
		for (int i = 0; i < n; i++) {
			for (int j = 0; j < n; j++) {
				if (j > i) break;
				scanf("%d", &triangle[i][j]);
			}
		}
		printf("%d\n", path(0, 0));
	}

	return 0;
}
```

## 결과 분석
- Bottom-up
  - 시간: 12ms
- Top-down + 메모이제이션
  - 시간: 8ms
