---
title: "[ALGOSPOT]PI"
excerpt: "DP"
search: true
categories:
  - PS
tags:
  - Algospot
---

# PI

## 문제 링크
- [https://algospot.com/judge/problem/read/PI](https://algospot.com/judge/problem/read/PI)

## 코드 분석
### 동적 계획법(메모이제이션)
- 첫 조각의 길이는 3, 4, 5 중의 하나이므로 각 경우마다 하나씩의 부분 문제를 해결한다, 이때 세 개의 부분 문제의 최적화(최소값)을 각각 구하면 전체 문제의 최적해가 된다.
1. 길이 3인 조각의 난이도 +3글자 빼고 나머지 수열에 대한 최적해

2. 길이 4인 조각의 난이도 +4글자 빼고 나머지 수열에 대한 최적해

3. 길이 5인 조각의 난이도 +5글자 빼고 나머지 수열에 대한 최적해
- begin: 부분 수열의 시작 위치, memorize(): 최소 난이도를 반환하는 함수, returnLevel(): 해당 수열의 난이도를 반환하는 함수
- ___memorize(begin) = min(memorize(begin + L) + returnLevel(N[begin...begin + L])), L: 3 ~ 5___
### returnLevel() 함수
- checkLevel() 함수
1. 수열의 길이 3, 4, 5에 대해서만 적용할 수 있는 함수이다.

2. 시간복잡도면에서 효율적이다. (소요시간: 24ms)
- classify() 함수
1. 모든 수열의 길이에 대해 적용 가능하다.

2. 시간이 오래 걸린다. (소요시간: 250ms)

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <string>
#include <cstring>
#include <cmath>
#include <limits.h>
using namespace std;
#define N 10001
#define min(a, b) ((a) < (b) ? (a) : (b))
string arr;
const int INF = 987654321;
int cache[N];

//1) arr[s]부터 size만큼 크기의 난이도를 반환
int checkLevel(int s, int size)
{
	if (size == 3) {
		int s1, s2, s3;
		s1 = arr[s]; s2 = arr[s + 1]; s3 = arr[s + 2];
		if (s1 == s2 && s2 == s3)
			return 1;
		int d1, d2;
		d1 = s2 - s1; d2 = s3 - s2;
		if (d1 == d2) {
			if (d1 == 1 || d1 == -1)
				return 2;
			else
				return 5;
		}
		if (s1 == s3)
			return 4;
	}
	else if (size == 4) {
		int s1, s2, s3, s4;
		s1 = arr[s]; s2 = arr[s + 1]; s3 = arr[s + 2]; s4 = arr[s + 3];
		if (s1 == s2 && s2 == s3 && s3 == s4)
			return 1;
		int d1, d2, d3;
		d1 = s2 - s1; d2 = s3 - s2; d3 = s4 - s3;
		if (d1 == d2 && d2 == d3) {
			if (d1 == 1 || d1 == -1)
				return 2;
			else
				return 5;
		}
		if (s1 == s3 && s2 == s4)
			return 4;
	}
	else if (size == 5) {
		int s1, s2, s3, s4, s5;
		s1 = arr[s]; s2 = arr[s + 1]; s3 = arr[s + 2];
		s4 = arr[s + 3]; s5 = arr[s + 4];
		if (s1 == s2 && s2 == s3 && s3 == s4 && s4 == s5)
			return 1;
		int d1, d2, d3, d4;
		d1 = s2 - s1; d2 = s3 - s2; d3 = s4 - s3; d4 = s5 - s4;
		if (d1 == d2 && d2 == d3 && d3 == d4) {
			if (d1 == 1 || d1 == -1)
				return 2;
			else
				return 5;
		}
		if ((s1 == s3 && s3 == s5) && s2 == s4)
			return 4;
	}
	return 10;
}

//2) arr[a,b]구간의 난이도를 반환한다.
int classify(int a, int b)
{
	//숫자 조각을 가져온다.
	string M = arr.substr(a, b - a + 1);
	//첫 글자만으로 이루어진 문자열과 같은으면 난이도 1
	if (M == string(M.size(), M[0])) return 1;
	//등차수열인지 검사한다.
	bool progressive = true;
	for (int i = 0; i < (int)M.size() - 1; i++)
		if (M[i + 1] - M[i] != M[1] - M[0])
			progressive = false;
	//등차수열이고 공차가 1 or -1 이면 난이도 2
	if (progressive && abs(M[1] - M[0]) == 1)
		return 2;
	//두 수가 번갈아 등장하는지 확인한다.
	bool alternating = true;
	for (int i = 0; i < (int)M.size(); i++)
		if (M[i] != M[i % 2])
			alternating = false;
	if (alternating) return 4;  //두 수가 번갈아 등장하면 난이도 4
	if (progressive) return 5;  //공차가 1 아닌 등차수열이면 난이도 5
	return 10;                  //이 외는 모두 난이도 10
}

//수열 arr[begin...]를 외우는 방법 중 난이도의 최소 합을 출력한다.
int memorize(int begin)
{
	//기저 사례: 수열의 끝에 도달했을 경우
	if (begin == arr.size()) return 0;
	//메모이제이션
	int &ret = cache[begin];
	if (ret != -1) return ret;
	ret = INF;
	for (int L = 3; L <= 5; ++L) {
		if (begin + L <= (int)arr.size())
			ret = min(ret, memorize(begin + L) + checkLevel(begin, L));              //방법 1)
			//ret = min(ret, memorize(begin + L) + classify(begin, begin + L - 1));  //방법 2)
	}
	return ret;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		memset(cache, -1, sizeof(cache));
		arr.clear();
		cin >> arr;
		printf("%d\n", memorize(0));
	}

	return 0;
}
```
