---
title: "LCS 알고리즘"
excerpt: "최신 업데이트 날짜: 2019-04-30"
date: 2019-04-30 14:00:00
categories:
  - Algorithm
tags:
  - Algorithm_Algorithm
search: true
toc: true
sidebar_main: true
---

LCS 알고리즘은 총 2가지로 나뉜다.
1. LCS(Longest Common Substring): 최장 공통 부분 문자열
2. LCS(Longest Common Subsequence): 최장 공통 부분 수열
이 둘의 차이점은 1번은 **연속적** 이고, 2번은 비연속적이다. 하지만 1번과 2번 모두 **동적 계획법** 으로 해결할 수 있다는 공통점을 가지고 있다. 먼저, 예를 보자.

```
ABCDEFGHI
BCDGHD
```

위 예제에서 1번과 2번의 결과는 아래와 같다.

1. ```BCD```
2. ```BCDGH```

최장 공통 부분 문자열은 연속적이어야 하기 때문에 총 2가지 경우가 있다. ```BCD```와 ```GH```이다. 여기서 BCD가 더 길기때문에 답이 된다.

최장 공통 부분 수열은 비연속적이기 때문에 문자가 서로 떨어져 있다고 해도 같다면 같은 수열에 속한다. 따라서, 답은 ```BCDGH```이 된다.

하지만 대부분 LCS 알고리즘이라고 하면 **Longest Common Subsequence** 로 즉, 최장 공통 부분 수열을 말한다.

## Longest Common Substring
최장 공통 부분 문자열은 최장 공통 부분 수열보다 간단하다. 연속적인 부분만 보면 되므로 O(N^2)으로 해결할 수 있다. 접근법은 위에서 말했듯이 동적 계획법으로 접근한다.

```
DP[i][j] = 문자열 S1의 위치 i, 문자열 S2의 위치 j일 때, 최장 공통 부분 문자열의 길이
```

```cpp
for (int i = 1; i <= S1.size(); ++i) {
  for (int j = 1; j <= S2.size(); ++j) {
    if (S1[i - 1] == S2[i - 1]) {
       DP[i][j] = DP[i - 1][j - 1] + 1;
       if (LCS < DP[i][j])
        LCS = DP[i][j];
    }
  }
}
```

## Longest Common Subsequence
최장 공통 부분 수열은 연속적이지 않은 부분을 모두 고려해야 하므로 최장 공통 부분 문자열보다 복잡하다. 하지만 이 역시 동적 계획법으로 해결 가능하며 DP(cache)배열의 역할 역시 같다.

```
DP[i][j] = 문자열 S1의 위치 i, 문자열 S2의 위치 j일 때, 최장 공통 부분 문자열의 길이
```

### Bottom-up 방식
먼저, 반복문을 사용하는 Bottom-up 방식의 동적 계획법을 살펴보자. 여기서 점화식은 아래와 같다.

```
if i == 0 or j == 0
  DP[i][j] = 0
else if S1[i] == S2[j]
  DP[i][j] = DP[i - 1][j - 1] + 1
else if S1[i] != S2[j]
  DP[i][j] = max(DP[i][j - 1], DP[i - 1][j])
```

위의 점화식에서 DP배열의 첫 번째에 0값을 삽입하는 것은 구현의 편의를 위해서이다. 이를 적용하여 아래의 예제를 풀어보자.

```
ACAYKP
CAPCAK
```

|  | 0 | A | C | A | Y | K | P |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| C | 0 | 0 | 1 | 1 | 1 | 1 | 1 |
| A | 0 | 1 | 1 | 2 | 2 | 2 | 2 |
| P | 0 | 1 | 1 | 2 | 2 | 2 | 3 |
| C | 0 | 1 | 2 | 2 | 2 | 2 | 3 |
| A | 0 | 1 | 2 | 3 | 3 | 3 | 3 |
| K | 0 | 1 | 2 | 3 | 3 | 4 | 4 |

위 예제를 점화식을 적용하여 직접 모두 풀어보면 위와 같은 2차원 배열을 얻을 수 있다. 위 예제의 LCS는 두 문자열의 끝이므로 답은 4인 것을 볼 수 있다.

```cpp
// DP 배열 0으로 초기화
for (int i = 1; i <= S1.size(); ++i) {
  for (int j = 1; j <= S2.size(); ++j) {
    if (S1[i] == S2[j])
      DP[i][j] = DP[i - 1][j - 1] + 1;
    else
      DP[i][j] = max(DP[i - 1][j], DP[i][j - 1]);
  }
}
```

### Top-down 방식
이번에는 재귀를 사용하여 해결해보자. 재귀를 이용할 때는 대부분 완전탐색의 코드에서 최적화를 해나아가는 방식을 사용한다. 그러면 먼저 완전 탐색 코드를 보자.

```cpp
void BruteForce(int i, int j, int lcs)
{
	if (i == s1.size() || j == s2.size()) {
		ans = max(ans, lcs);
		return;
	}

	if (s1[i] == s2[j])
		BruteForce(i + 1, j + 1, lcs + 1);
	BruteForce(i + 1, j, lcs);
	BruteForce(i, j + 1, lcs);
}
```

위 코드는 S1, S2 문자열이 있을 때, S1 문자열의 위치부터 옮기면서 진행하며 만약 현재 S1, S2 문자열의 위치에서 해당 문자가 서로 같다면, 두 문자열 모두 위치를 한 칸 옮긴다. 그리고 다시 한 문자열의 위치를 옮겨나간다.(S1 문자열의 위치를 끝까지 옮긴 후에는 S2를 한 칸 옮기고 다시 S1의 위치를 옮기는 방식으로 동작한다.)

완전 탐색으로는 문자열의 길이가 조금만 길어져도 시간이 매우 오래 걸린다. 그리고 위 코드를 직접 트리로 표현하면 수 많은 중복이 발생한다는 것을 알 수 있다. 이 때 사용하는 최적화가 동적 계획법이다. 메모이제이션을 사용한 코드는 아래와 같다.

```cpp
int cache[MAX_LEN][MAX_LEN];
int Memoization(int i, int j)
{
	if (i == s1.size() || j == s2.size())
		return 0;
	if (s1[i] == s2[j]) return solve(i + 1, j + 1) + 1;

	int& ret = cache[i][j];
	if (cache[i][j] != -1) return ret;

	return ret = max(solve(i + 1, j), solve(i, j + 1));
}
```
