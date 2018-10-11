---
title: "[ALGOSPOT]QUANTIZE"
search: true
categories:
  - PS
tags:
  - Algospot
---

# QUANTIZE

## 문제 링크
- [https://algospot.com/judge/problem/read/QUANTIZE](https://algospot.com/judge/problem/read/QUANTIZE)

## 코드 분석
- 완전 탐색
  - 시간복잡도: O(1000C10^n)이고, 최대 연산수 ```1000C10^100```이므로 시간안에 풀 수 없다.
- 동적 계획법(메모이제이션)
  - ___주어진 수열을 오름차순 or 내림차순으로 정렬하면, 같은 숫자로 양자화되는 숫자들은 항상 인접해 있다!___
    - 예를들어, a < b에 대해 a에 대응되는 숫자가 b에 대응되는 숫자보다 커서는 안된다.
    - 이는 몇 개의 작은 입력들을 풀어보면 알 수 있는 조건이며, 시간내에 풀기 위해 알아야하는 중요한 조건이다.
  - 기본 테스트 케이스의 한 예를 사용하면, {1, 4, 6, 744, 755, 777, 890, 897, 902} => {1, 4, 6}: 4, {744, 755, 777}: 759, {890, 897, 902}: 896 로 대응된다.
  - 이를 형식화하면, from번째 이후의 숫자들을 parts개의 묶음으로 묶을 때, 최소 오류 제곱 합을 반환하는 함수 quantize(from, parts)가 있다.
  - ___quantize(from, parts) = min[minError(from, from + size - 1) + quantize(from + size, parts - 1)], size: 1 ~ n-from___
  - minError(a,b): a번째 숫자부터 b번째 숫자까지 하나의 수로 표현했을 때의 최소 오류를 반환하는 함수
  1. 주어진 구간을 어떤 수로 표현해야 할지 결정하기
  2. 결정한 수 m으로 해당 구간을 표현했을 때 오차를 계산하기
    - 위의 역할을 완전 탐색으로 구현하여도 시간안에 해결할 수 있지만, 밑의 구현 코드는 좀 더 복잡하며 효율적인 방법을 사용한다.
    - ___이는 오차 제곱의 합을 미분하여 값을 도출하며, 이를 부분 합 계산 방식으로 시간복잡도 O(1)의 방법으로 푸는 방법이며, 이 설명은 추후 추가할 예정이다.___
  시간복잡도: 부분 문제의 수 ns개를 각각 n번 계산해야 하기 때문에 총 O(sn^2)이다.

## 결과 코드
- 링크:
```
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
using namespace std;
#define N 10001
#define min(a,b) ((a)<(b)?(a):(b))

const int INF = 987654321;
//A[]: 양자화해야 할 수열, 정렬한 상태
//pSum[]: A[]의 부분합을 저장한다. pSum[i]는 A[0]...A[i]의 합
//qSuSum[]: A[]제곱의 부분합을 저장한다. pSqSum[i]는 A[0]^2...A[i]^2의 합
int n;
int A[N], pSum[N], pSqSum[N];

//A를 정렬하고 가가 부분합을 계산한다.
void precalc()
{
	sort(A, A + n);
	pSum[0] = A[0];
	pSqSum[0] = A[0] * A[0];
	for (int i = 1; i < n; i++) {
		pSum[i] = pSum[i - 1] + A[i];
		pSqSum[i] = pSqSum[i - 1] + A[i] * A[i];
	}
}

//A[lo]...A[hi] 구간을 하나의 숫자로 표현할 때 최소 오차 합을 반환한다.
int minError(int lo, int hi)
{
	//부분합을 이용해 A[lo] ~ A[hi]까지의 합을 구한다.
	int sum = pSum[hi] - (lo == 0 ? 0 : pSum[lo - 1]);
	int sqSum = pSqSum[hi] - (lo == 0 ? 0 : pSqSum[lo - 1]);
	//평균을 반올림한 값으로 이 수 들을 표현한다.
	int m = int(0.5 + (double)sum / (hi - lo + 1));
	//sum(A[i] - m)^2를 전개한 결과를 부분 합으로 표현
	int ret = sqSum - 2 * m *sum + m *m *(hi - lo + 1);
	return ret;
}

int cache[N][11];
int quantize(int from, int parts)
{
	//기저 사례: 모든 숫자들 양자화했을 떄
	if (from == n) return 0;
	//기저 사례: 숫자는 아직 남았는데 더 묶을 수 없을 때 아주 큰 값으르 반환한다.
	if (parts == 0) return INF;
	int &ret = cache[from][parts];
	if (ret != -1) return ret;
	ret = INF;
	//조각의 길이를 변화시켜 가며 최소값을 찾는다.
	for (int partSize = 1; from + partSize <= n; partSize++)
		ret = min(ret, minError(from, from + partSize - 1)
			+ quantize(from + partSize, parts - 1));
	return ret;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		memset(cache, -1, sizeof(cache));
		memset(A, 0, sizeof(A));
		memset(pSum, 0, sizeof(pSum));
		memset(pSqSum, 0, sizeof(pSqSum));
		int s;
		scanf("%d %d", &n, &s);
		for (int i = 0; i < n; i++)
			scanf("%d", &A[i]);
		precalc();
		printf("%d\n", quantize(0, s));
	}

	return 0;
}
```

[HOME](https://codemcd.github.io/)
