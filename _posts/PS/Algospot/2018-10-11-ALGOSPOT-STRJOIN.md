---
title: "[ALGOSPOT]STRJOIN"
excerpt: "그리디"
search: true
sidebar_main: true
categories:
  - Algorithm
tags:
  - Algorithm_Algospot
---

# 문자열 합치기

## 문제 링크
- [https://algospot.com/judge/problem/read/STRJOIN](https://algospot.com/judge/problem/read/STRJOIN)

## 코드 분석
- 그리디 알고리즘
- 문자열 길이가 가장 짧은 2개를 계속 더해나간다.
  - 더한 값 역시 문자열 길이가 저장되어 있는 배열에 갱신해야 한다.
- vector로 구현
  - vector의 멤버함수인 push_back과 pop_back을 사용하기 위해 내림차순으로 정렬하여 구현하였다.
  - 시간 복잡도: 내림차순 정렬```O(NlogN)``` + 문자열 길이 2개 더하고 vector 갱신하기```O(N)``` = ```O(N^2 * logN)```
- 우선순위 큐로 구현
  - 시간복잡도: 우선순위 큐```O(logN)``` + 문자열 길이 2개 더하고 큐 갱신```O(N)``` = ```O(NlogN)```
  - 허프만 압축 알고리즘을 응용한다.

## 결과 코드
- vector로 구현한 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <vector>
#include <algorithm>
using namespace std;
vector<int> len;

//내림차순
bool compare(const int a, const int b)
{ return a > b; }

int main()
{
	int tc; scanf("%d", &tc);
	while (tc--) {
		int n; scanf("%d", &n);
		for (int i = 0; i < n; i++) {
			int num; scanf("%d", &num);
			len.push_back(num);
		}
		int vlen = len.size();
		//내림차순 정렬
		sort(len.begin(), len.end(), compare);
		/*for (int i = 0; i < vlen; i++)
			printf("%d ", len[i]);*/
		int ans = 0, num1, num2, sum;
		//문자열 길이가 2개 이상이면 계속 더해나간다.
		while (vlen > 1) {
			//문자열 길이가 가장 짧은 2개를 뽑아서 더한다.
			num1 = len[vlen - 1];
			num2 = len[vlen - 2];
			len.pop_back();
			len.pop_back();
			sum = num1 + num2;
			ans += sum;
			//더한 값 역시 vector에 추가한다.
			len.push_back(sum);
			//vector 길이를 갱신한다.
			vlen = len.size();
			//다시 내림차순 정렬을 한다.
			sort(len.begin(), len.end(), compare);
		}
		printf("%d\n", ans);
		len.clear();
	}

	return 0;
}
```
