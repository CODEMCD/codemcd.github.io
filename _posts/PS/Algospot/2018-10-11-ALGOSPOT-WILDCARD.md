---
title: "[ALGOSPOT]WILDCARD"
search: true
categories:
  - PS
tags:
  - Algospot
---

# WILDCARD

## 문제 링크
- [https://algospot.com/judge/problem/read/WILDCARD](https://algospot.com/judge/problem/read/WILDCARD)

## 코드 분석
### 완전 탐색: 4가지 경우
- s[pos]와 w[pos]가 대응되지 않는다.: 대응 실패
- w 끝에 도달했다.: 패턴에 *가 하나도 없는 경우, 패턴과 문자열의 길이가 정확히 같아야 대응 할 수 있다.
- s 끝에 도달했다.: 패턴은 남아있지만 문자열은 끝난 경우, 남은 패턴이 모두 *면 대응 가능하지만 아니면 대응 실패이다.
- __w[pos]가 *인 경우__: *가 몇 글자에 대응될지 모르기 때문에, 0 글자부터 남은 문자열의 길이까지를 순회하며 모든 가능성을 검사해야한다.이때 w는 pos+1이후를 패턴 w'으로 하고, s의 pos + skip(현재 인덱스(pos) 이후 문자열 끝까지) 이후를 문자열 s'로 하여 match(w', s')로 재귀 호출했을 때 답이 하나라도 참이면 대응 가능하다.

### DP
- w와 s는 각각 최대 101개이다.
- match()가 ```101 * 101 = 10201```번 이상 호출되었다면 비둘기집의 원리에 따라 어떤 부분 문제가 반드시 여러 번 계산되고 있다는 뜻이다.
- 중복되는 계산은 메모이제이션을 사용하여 해결 가능하다.
- w는 항상 전체 패턴 W의 접미사이기 때문에 w의 길이가 결정되면 w 또한 결정된다. 이를 이용하여 101 X 101 크기의 배열에 모든 부분 문제를 저장할 수 있다.

## 결과 코드
- 완전 탐색

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;
#define N 1001
vector<string> res;

//와일드카드 패턴 w가 문자열 s에 대응되는지 여부를 반환한다.
bool match(const string &w, const string &s)
{
	//w[pos]와 s[pos]를 맞춰나간다.
	int pos = 0;
	while (pos < (int)s.size() && pos < (int)w.size() &&
		(w[pos] == '?' || w[pos] == s[pos]))
		++pos;
	//더이상 대응할 수 없으면 왜 while문이 끝났는지 확인한다.
	//2. 패턴 끝에 도달해서 끝난 경우: 문자열도 끝났어야 대응됨
	if (pos == w.size())
		return pos == s.size();
	//4. *를 만나서 끝난 경우: *에 몇 글자를 대응해야 할지 재귀 호출하면서 확인한다.
	if (w[pos] == '*')
		for (int skip = 0; pos + skip <= (int)s.size(); ++skip)
			//하나라도 대응되는 것이 있으면 TRUE 반환
			if (match(w.substr(pos + 1), s.substr(pos + skip)))
				return true;
	//이 외의 경우에는 모두 대응되지 않는다.
	return false;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		string wild;
		cin >> wild;
		int n;
		scanf("%d", &n);
		while (n--) {
			string file;
			cin >> file;

			if (match(wild, file))
				res.push_back(file);
		}
		sort(res.begin(), res.end());
		for (int i = 0; i < (int)res.size(); i++)
			cout << res[i] << endl;
		res.clear();
	}

	return 0;
}
```

- DP

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <vector>
#include <string>
#include <algorithm>
using namespace std;
#define N 101
vector<string> res;
//-1: 아직 답이 계산되지 않음
//1: 해당 입력들이 서로 대응됨
//0: 해당 입력들이 서로 대응되지 않음
int cache[N][N];
//패턴과 문자열
string W, S;
//와일드카드 패턴 W[w...]가 문자열 S[s...]에 대응되는지 여부를 반환
int matchMemoized(int w, int s)
{
	//메모이제이션
	int &ret = cache[w][s];
	if(ret != -1) return ret;
	//W[w]와 S[s]를 맞춰나간다.
	while (s < (int)S.size() && w < (int)W.size() &&
		(W[w] == '?' || W[w] == S[s])) {
		++w;
		++s;
	}
	//더이상 대응할 수 없으면 왜 while문이 끝났는지 확인한다.
	//2. 패턴 끝에 도달해서 끝난 경우: 문자열도 끝났어야 참이다.
	if (w == W.size()) return ret = (s == (int)S.size());
	//4. *를 만나서 끝난 경우: *에 몇 글자를 대응해야 할지 재귀 호출하면서 확인한다.
	if (W[w] == '*')
		for (int skip = 0; skip + s <= (int)S.size(); ++skip)
			if (matchMemoized(w + 1, s + skip))
				return ret = 1;
	//3. 이 외의 경우에는 모두 대응되지 않는다.
	return ret = 0;
}

int main(void)
{
	int tc;
	scanf("%d", &tc);
	while (tc--) {
		cin >> W;
		int n;
		scanf("%d", &n);
		while (n--) {
			memset(cache, -1, sizeof(cache));
			cin >> S;

			if (matchMemoized(0, 0))
				res.push_back(S);
			S.clear();
		}
		sort(res.begin(), res.end());
		for (int i = 0; i < (int)res.size(); i++)
			cout << res[i] << endl;
		res.clear();
		W.clear();
	}

	return 0;
}
```

## 결과 분석
- 완전 탐색
	- 소요 시간: 44ms
- DP
	- 소요 시간: 4ms
