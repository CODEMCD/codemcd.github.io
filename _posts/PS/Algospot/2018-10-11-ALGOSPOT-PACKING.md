---
title: "[ALGOSPOT]PACKING"
excerpt: "DP"
search: true
sidebar_main: true
categories:
  - PS
tags:
  - Algospot
---

# 여행 짐 싸기

## 문제 링크
- [https://algospot.com/judge/problem/read/PACKING](https://algospot.com/judge/problem/read/PACKING)

## 코드 분석
### 1. 완전 탐색
- 시간 복잡도: 각 물건에 대해 가져가거나 말거나 두 가지의 선택이 있다.
  - ```O(2^N)```
- ```pack(items)``` = 지금까지 고른 문건들의 목록이 items에 주어질 때, 남은 용량을 채워 얻을 수 있는 최대 절박도의 합

### 2. 동적 계획법
- 완전 탐색에서 메모이제이션을 적용해보자.
  - items를 넣고 남은 용량에 담을 수 있는 물건들의 절박도 합만을 반환하도록 ```pack()```을 바꾼다.
  - 그 결과, 지금까지 고른 물건들의 목록은 상관이 없어진다.
  - 중요한 것은 마지막으로 고른 물건의 번호(같은 물건을 두 번 고르면 안되므로)와 캐리어에 남아있는 용량이다.
- **capacity**: 남은 용량, **item**: 물건의 번호, **volume[item]**: 해당 물건의 부피, **need[item]**: 해당 물건의 절박도
- ```pack(capacity, item)``` = 캐리어에 용량이 capacity만큼 남았을 때, item 이후의 물건들을 싸서 얻을 수 있는 최대 절박도
  - 해당 물건을 가져가는 경우: ```pack(capacity - volume[item], item + 1) + need[item]```
  - 해당 물건을 가져가지 않는 경우: ```pack(capacity, item + 1)```
- 시간 복잡도: 용량 w, 물건의 개수 n 일때, 부분 문제의 수는 nw개이고 각 부분 문제를 해결하는데 걸리는 시간은 상수 시간이다.
  - ```O(nw)```

### 3. 답 추적하기
- 각 부분 문제에서 선택지가 두 가지밖에 없으므로 따로 선택으르 저장하지 않고도 답을 역추적할 수 있다.
- ```pack(capacity, item)``` 부분 문제에서 item을 선택했는지를 알고 싶으면 ```pack(capacity, item + 1)```과 ```pack(capacity, item)```이 같은지 비교하면 된다.
  - 만약 두 값이 같다면, item을 선택하지 않고도 최대 절박도를 얻을 수 있다는 말이므로 item을 무시한다.
  - 다르면, 목록에 item을 추가한다.

## 결과 코드

```cpp
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>
#include <cstdio>
#include <cstring>
#include <vector>
#include <string>
using namespace std;
#define max(a,b) ((a) > (b) ? (a) : (b))
#define N 101
int n, w;
int cache[1001][N];

//물건의 정보를 저장할 구조체
typedef struct _product
{
	string name;
	int volume;
	int need;
}product;

vector<product> pv;

//캐리어에 남은 용량이 capacity일때, item 이후의 물건들을
//담아 얻을 수 있는 최대 절박도의 합을 반환한다.
int pack(int capacity, int item) {
	//기저 사례: 더 담을 물건이 없을 때
	if (item == n) return 0;
	int &ret = cache[capacity][item];
	if (ret != -1) return ret;
	//이 물건을 담지 않을 경우
	ret = pack(capacity, item + 1);
	//이 물건을 담을 경우
	if (capacity >= pv[item].volume)
		ret = max(ret, pack(capacity - pv[item].volume, item + 1) +
			pv[item].need);
	return ret;
}

//pack(capacity, item)이 선택한 물건들의 목록을 packed에 저장한다.
void reconstruct(int capacity, int item, vector<string> &packed) {
	//기저 사례: 모든 물건을 다 고려했을 때
	if (item == n) return;
	if (pack(capacity, item) == pack(capacity, item + 1))
		reconstruct(capacity, item + 1, packed);
	else {
		packed.push_back(pv[item].name);
		reconstruct(capacity - pv[item].volume, item + 1, packed);
	}
}

//초기화
void init() {
	memset(cache, -1, sizeof(cache));
	pv.clear();
}

int main()
{
	int tc; scanf("%d", &tc);
	while (tc--) {
		init();
		scanf("%d %d", &n, &w);
		for (int i = 0; i < n; i++) {
			product in;
			cin >> in.name;
			scanf("%d %d", &in.volume, &in.need);
			pv.push_back(in);
		}

		vector<string> packed;
		reconstruct(w, 0, packed);
		int psize = packed.size();
		printf("%d %d\n", pack(w, 0), psize);
		for (int i = 0; i < psize; i++)
			cout << packed[i] << endl;
	}

	return 0;
}
```
