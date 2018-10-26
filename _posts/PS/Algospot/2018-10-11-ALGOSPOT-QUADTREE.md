---
title: "[ALGOSPOT]QUADTREE"
excerpt: "분할 정복"
search: true
sidebar_main: true
categories:
  - PS
tags:
  - Algospot
---

# QUADTREE

## 문제 링크
- [https://algospot.com/judge/problem/read/QUADTREE](https://algospot.com/judge/problem/read/QUADTREE)

## 코드 분석
- 출처: 알고리즘 문제해결전략, 분할정복
- 재귀 호출을 이용해 네 부분을 각각 상하로 뒤집은 결과를 얻은 뒤, 이들을 병합한다.
- STL의 string 함수 사용
- STL의 문자열에서 지원하는 반복자(iterator) 활용
- 시간복잡도 분석: O(n)
  - 주어진 문자열 길이: n
  - 재귀 함수를 호출할 때 마다 주어진 문자열의 한 글자씩을 사용한다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <string>
using namespace std;

string reserve(string::iterator &it)
{
	char head = *it;
	++it;
	if (head == 'b' || head == 'w')
		return string(1, head);
	string upperLeft = reserve(it);
	string upperRight = reserve(it);
	string lowerLeft = reserve(it);
	string lowerRight = reserve(it);

	//각각 위와 아래 조각들의 위치를 바꾼다.
	return string("x") + lowerLeft + lowerRight +
		upperLeft + upperRight;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);

	while (tc--) {
		string map;
		cin >> map;

		string::iterator it = map.begin();
		cout << reserve(it) << endl;
	}

	return 0;
}
```

## 결과 분석
- 수행시간: 4ms
