---
title: "[Algorithm]KMP 알고리즘"
excerpt: "문자열 검색 알고리즘"
search: true
categories:
  - Algorithm
tags:
  - 유명 알고리즘
toc: true
---

# KMP 알고리즘
- 풀네임은 커누스-모리스-프랫(Knuth-Morris-Pratt) 알고리즘으로, 문자열 검색 알고리즘이다.

## 미리 알아 둘점
- 문자열 S의 길이는 ```|S|```로 표기
- 문자열 S의 i번부터 j번까지 구성된 부분 문자열을 ```S[i...j]```로 표기
- 문자열 S의 0번부터 a번까지 구성된 부분 문자열을 접두사(prefix)라 부르고, ```S[...a]```로 표기
- 문자열 S의 b번부터 끝까지 구성된 부분 문자열을 접미사(suffix)라 부르고, ```S[b...]```로 표기

## 기본 개념
- 기본적인 검색 알고리즘은 한 문자열의 시작점을 하나씩 옮겨가면서 찾을 문자열을 검색하는 것이다.
- KMP 알고리즘은 시작점을 하나씩 옮기는 것이 아니라 최적화 과정을 거쳐 옮길 칸 수를 정한다.
- 예를 들어, N = "aabaabac"를 찾는 경우가 있다.
  - H 문자열의 해당 부분 문자열인 ```H[i...i + 6]```이 "aabaaba"까지 일치한다.
  - 그렇다면, i + 1에서 시작하는 N은 H와 일치할 수 없다는 것을 알 수 있다.
  - 가능성이 있는 위치는 i + 3, i + 6 이라는 것을 아래의 그림을 보면 알 수 있다.

![kmp](https://user-images.githubusercontent.com/34755287/46916211-69de1f00-cff2-11e8-816e-93fc2fe2346a.JPG)

## 다음 시작 위치 찾기
- 다음 시작 위치를 어떻게 찾는지 예제 그림을 보며, 알아보자

![kmp2](https://user-images.githubusercontent.com/34755287/46916212-6b0f4c00-cff2-11e8-92e4-2ff9a6c7885c.JPG)

- 위 그림에서 시작 위치 i일때, H와 N을 맞춰보면 matched글자가 일치하고 다음 글자가 불일치하는 상황을 볼 수 있다.
- matched글자가 일치했기 때문에, N의 접두사 ```N[...matced - 1]```가  ```H[i...i + matched - 1]```와 일치했음을 알 수 있다.(회색으로 칠해진 부분)
- 시작 위치 i + k가 답이 될려면, B와 C가 서로 같아야 하며, 결과적으로 A와 C도 같아야 한다.(굵은 선 네모 부분)
  - ```A = C```는 ```N[...matced - 1]```의 접두사이기도 하고 접미사이기도 한다.
  - 시작 위치 i + k가 답을 찾을 수 있기 위해서는 ```N[...matced - 1]```의 길이 matched - k인 접두사와 접미사가 같아야 한다.
- 정리하면, 답이 될 수 있는 바로 다음 위치를 찾기 위해서는 N의 각 접두사에 대해 접두사도 되고 접미사도 되는 문자열의 최대 길이를 계산해야 한다.
  - 위의 예제 "aabaaba"에서 접두사도 되고 접미사도 되는 문자열은 "aaba"와 "a" 두가지 있다.
  - 여기서 최대 길이 문자열은 "aaba"이기 때문에, 시작 위치를 3만큼 옮겨주었던 것이다.

## KMP 알고리즘 구현
- 다음 시작 위치를 찾는 배열을 다음과 같이 정의한다.
  - ```pi[i]``` = ```N[...i]```의 접두사도 되고 접미사도 되는 문자열의 최대 길이
- ```pi[]```는 어디까지 일치했는지가 주어질 때 다음 시작 위치를 알려주기 때문에, 이를 흔히 부분 일치 테이블(partial match table) 또는 실패 함수(faulure function)이라 부른다.
  - 찾는 문자열 "aabaabac"의 각 접두사에 대한 부분 일치 테이블은 밑에와 같이 계산된다.

  | i |  N[...i] | 접두사이면서 접미사인 최대 문자열 | pi[i] |
  |:-:|:--------:|:---------------------------------:|:-----:|
  | 0 |     a    |               (없음)              |   0   |
  | 1 |    aa    |                 a                 |   1   |
  | 2 |    aab   |               (없음)              |   0   |
  | 3 |   aaba   |                 a                 |   1   |
  | 4 |   aabaa  |                 aa                |   2   |
  | 5 |  aabaab  |                aab                |   3   |
  | 6 |  aabaaba |                aaba               |   4   |
  | 7 | aabaabac |               (없음)              |   0   |

- KMP 알고리즘 동작 과정
  - 초기에는 단순한 알고리즘과 같이 시작 위치 0부터 시작한다.
  - 만약 matched글자가 일치한 후 불일치가 발생한다면, 위 그림의 A의 길이는 ```pi[matched - 1]```이다.
  - 시작 위치를 ```matched - pi[matched - 1]```만큼 증가 시킨다.
    - 위와 같이 동작하면, **시작 위치를 움직인 이후 첫 글자부터 다시 대응시켜 나갈 필요가 없다는 것이 중요한 관점이다.**
    - 새로운 위치에서 비교를 시작하더라도 N의 첫 ```pi[matched - 1]```글자는 대응되는 H의 글자와 일치하기 때문이다.(위 그림에서 B와 C가 서로 같기 때문이다.)
  - matched를 ```pi[matched - 1]```로 변경하고 비교를 계속한다.
  - 답을 찾은 경우에는 현재 시작 위치를 답의 목록에 추가해주고, 불일치가 발생한 경우와 같이 다음 시작 위치에서부터 다시 검색을 한다.
  - ```matched = 0```이면 한 글자도 일치하지 않았다는 의미이므로, 바로 다음 시작 위치에서 처음부터 검색을 시작한다.
- 구현 코드
  - 참고사항으로, 부분 일치 테이블의 구현과 관련된 점은 아래에서 설명한다.

```cpp
//'짚더미' H의 부분 문자열로 '바늘' N이 출현하는 시작 위치들을 모두 반환한다.
vector<int> kmpSearch(const string &H, const string &N) {
	int n = H.size(), m = N.size();
	vector<int> ret;
	//pi[i] = N[...i]의 접미사도 되고 접두사도 되는 문자열의 최대 길이
	vector<int> pi = getPartialMatch(N);
	//begin = matched = 0 에서부터 시작
	int begin = 0, matched = 0;
	while (begin <= n - m) {
		//만약 짚더미의 해당 글자가 바늘의 해당 글자와 같다면
		if (matched < m && H[begin + matched] == N[matched]) {
			++matched;
			//결과적으로 m글자가 모두 일치했다면 답에 추가한다.
			if (matched == m) ret.push_back(begin);
		}
		else {
			//예외: matched가 0인 경우에는 다음 칸에서부터 계속
			if (matched == 0)
				++begin;
			else {
				begin += matched - pi[matched - 1];
				//begin을 옮겼다고 처음부터 다시 비교할 필요가 없다.
				//옮긴 후에도 pi[matched - 1]만큼은 항상 일치하기 때문이다.
				matched = pi[matched - 1];
			}
		}
	}
	return ret;
}
```
- 시간 복잡도
  - getPartialMatch() 시간 복잡도는 아직 고려하지 않는다.
  - while문에서 ```begin + matched``` 는 절대 감소하지 않는다.
    - matched가 감소하면 그만큼 begin이 증가하기 때문이다.
  - 따라서, 최대 시간 복잡도는 ```O(|H|)```입니다.

## 부분 일치 테이블 생성하기
### 1. 간단한 알고리즘
- N의 각 접두사에 대해 가능한 모든 답을 시도한다.
- 길이 p인 접두사 ```N[...p - 1]```이 주어졌을때, 길이 ```p - 1, p - 2, ...```들을 순회하며 이들이 ```N[... p - 1]```의 접미사가 되는지 확인한다.
- 모든 접두사에 대해 한꺼번에 계산한다면, 시간 복잡도는 ```O(|N|^2)```이다.
- 구현 코드
```
//N에서 자기 자신을 찾으면서 나타나는 부분 일치를 이용해 pi[]를 계산한다.
//pi[i] = N[...i]의 접미사도 되고 접두사도 되는 문자열의 최대 길이
vector<int> getPartialMatchNaive(const string &N) {
	int m = N.size();
	vector<int> pi(m, 0);
	//단순한 문자열 검색 알고리즘을 구현한다.
	for (int begin = 1; begin < m; ++begin) {
		for (int i = 0; i < m; ++i) {
			if (N[begin + i] != N[i]) break;
			//i + 1글자가 서로 대응되었다.
			pi[begin + i] = max(pi[begin + i], i + 1);
		}
	}
	return pi;
}
```

### 2. KMP 알고리즘 활용
- 고려 사항
  - begin을 옮길 때 이전에 계산한 pi[] 값을 사용한다.
    - 현재 matched글자가 일치했다면 pi[matched - 1]는 항상 계산된 뒤임을 증명할 수 있기 때문이다.
  - pi[]의 각 원소는 최대 한 번만 변경되기 때문에 위의 간단한 알고리즘에서 처럼 max()연산을 해줄 필요가 없다.
- 구현 코드

```cpp
vector<int> getPartialMatch(const string &N) {
	int m = N.size();
	vector<int> pi(m, 0);
	//KMP로 자기 자신을 찾는다.
	//N을 N에서 찾는다. begin = 0이면 자기 자신을 찾아버리니까 안됨!
	int begin = 1, matched = 0;
	//비교할 문자가 N의 끝에 도달할 때까지 찾으면서 부분 일치를 모두 기록한다.
	while (begin + matched < m) {
		if (N[begin + matched] == N[matched]) {
			++matched;
			pi[begin + matched - 1] = matched;
		}
		else {
			if (matched == 0)
				++begin;
			else {
				begin += matched - pi[matched - 1];
				matched = pi[matched - 1];
			}
		}
	}
	return pi;
}
```
- 시간 복잡도: ```O(|N|)```
