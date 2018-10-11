---
title: "[ALGOSPOT]DRAGON"
search: true
categories:
  - PS
tags:
  - Algospot
last_modified_at: 2018-10-09T18:04+09:00
---

# 드래곤 커브

## 문제 링크
- <https://algospot.com/judge/problem/read/DRAGON>

## 코드 분석
### k번째 답을 계산하는 동적 계획법
- 더 간단한 문제 풀기
  - 드래곤 커브 문자열의 일부를 계산하는 문제 대신 문자열 중 주어진 위치의 한 글자만을 계산하는 문제로 바꾼다.
  - 이 문제를 빠르게 풀 수 있다면 l번 반복만 한다면 문제를 해결할 수 있다.
  - 결과적으로, n세대 드래곤 커브의 p번째 글자를 찾는 것으로 변경한다.
- p번째 글자를 찾는 함수
  - 먼저 전체 드래곤 커브 문자열을 생성하는 알고리즘을 재귀적인 방법으로 구현한다.
  - ```curve(seed, generations)``` = 초기 문자열 seed를 generations세대 진화시킨 결과를 출력한다.
  - 위 함수에서 p번째 글자만을 출력하기 위해 skip 전역변수(건너뛰어야 하는 글자 수)를 두어, 문자열 혹은 문자를 출력할 때마다 skip과 출력할 부분의 길이를 비교한다.
- 계산 결과 미리 계산하기(동적 계획법)
  - p번째 글자를 출력하는 재귀 호출 코드를 최적화하려면, 재귀 호출 때마다 몇 글자를 출력할지를 미리 알고 skip과 이 값을 비교할 수 있어야 한다.
  - genrations 세대 값의 크기에 상관 없이, 이전 세대의 드래곤 커브를 계속 유지하며 확장하기 때문에 skip 값의 길이 만큼의 generations 세대까지만 가면 된다.
  - ```curve(seed, generations)```함수에서 seed는 'X+TF' 혹은 'FX-Y'로 확장하기 때문에 간단히 동적 계획법 알고리즘을 만들 수 있다.
    - xLength(n) = 문자열 "X"를 n세대  진화시킨 결과의 길이 반환
    - yLength(n) = 문자열 "Y"를 n세대  진화시킨 결과의 길이 반환
    - xLength(n) = xLength(n - 1) + yLength(n - 1) + 2
    - yLength(n) = xLength(n - 1) + yLength(n - 1) + 2
    - 최적화: length(n) = length(n - 1) * 2 + 2
- 시간 복잡도
  - 재귀 호출 함수가 최대 n번 호출되고, 출력 길이 l의 최대 값이 50이므로, ```O(n * 50)```이다.
  - 결과적으로, ```O(n)```이다.

## 결과 코드
```
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <string>
#include <cassert>
using namespace std;
#define min(a,b) ((a) < (b) ? (a) : (b))
#define N 51
//오버 플로 방지
const int MAX = 1000000000 + 51;
//length[i] = X나 Y를 i번 치환 후의 길이
int length[N];

//계산 결과를 미리 계산하는 동적 계획법 알고리즘
void precalc() {
	length[0] = 1;
	for (int i = 1; i < N; ++i)
		length[i] = min(MAX, length[i - 1] * 2 + 2);
}

const string EXPAND_X = "X+YF";
const string EXPAND_Y = "FX-Y";

//dragonCurve를 generations 진화시킨 결과에서 skip번째 문자를 반환한다.
char expand(const string &dragonCurve, int generations, int skip) {
	//기저 사례
	if (generations == 0) {
		//assert(skip < (int)dragonCurve.size());
		return dragonCurve[skip];
	}
	for (int i = 0; i < (int)dragonCurve.size(); ++i) {
		//문자열이 확장되는 경우
		if (dragonCurve[i] == 'X' || dragonCurve[i] == 'Y') {
			if (skip >= length[generations])
				skip -= length[generations];
			else if (dragonCurve[i] == 'X')
				return expand(EXPAND_X, generations - 1, skip);
			else
				return expand(EXPAND_Y, generations - 1, skip);
		}
		//확장되진 않지만 건너뛰어야 할 경우
		else if (skip > 0)
			--skip;
		//답을 찾은 경우
		else
			return dragonCurve[i];
	}
	return '#';  //이 줄은 수행되지 않음
}

int main()
{
	int tc; scanf("%d", &tc);
	precalc();
	while (tc--) {
		int n, p, l; scanf("%d %d %d", &n, &p, &l);
		string seed = "FX";
		for (int skip = p - 1; skip < p + l - 1; ++skip)
			printf("%c", expand(seed, n, skip));
		printf("\n");
	}

	return 0;
}
```
