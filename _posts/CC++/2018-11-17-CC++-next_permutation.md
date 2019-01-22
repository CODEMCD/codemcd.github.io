---
title: "[C++ STL] next_permutation"
excerpt: "next_permutation STL 함수 이해"
categories:
  - Language
tags:
  - Language_CCpp
search: true
toc: true
sidebar_main: true
---

# next_permutation
```next_permutation``` 함수는 원래 순서를 다음 사전 순서의 순열로 재배치해주는 함수이다. (사전 순서는 오름차순이라고 볼 수 있다.)

## 구문

```cpp
template<class BidirectionalIterator>
  bool next_permutation(
    BidirectionalIterator _First,
    BidirectionalIterator _Last
  );
//배치 순서를 직접 지정해줄 수 있다.
template<class BidirectionalIterator, class BinaryPredicate>
  bool next_permutation(
    BidirectionalIterator _First,
    BidirectionalIterator _Last,
    BinaryPredicate _Comp
  );
```

- ```_First```: 다음 순열로 배치할 배열의 첫 번째 원소를 가르키는 반복자.
- ```_Last```: 다음 순열로 배치할 배열의 마지막 원소를 가르키는 반복자.
- ```_Comp```: 두 개의 인수를 사용하여 순서 조건이 만족하면 true, 만족하지 않으면 false를 반환한다.
- 반환값: 다음 순열이 현재 순열보다 사전 편찬 상 다음 순서이면 true, 사전 편찬 상 가장 작은 순서이면 false를 반환한다.
> 반환값을 통해 주의할 점은 순열을 구할 초기 배열이 오름차순으로 정렬되어 있다면 모든 순열을 순조롭게 구할 수 있지만, 오름차순이 아니라면 중간에 사전 편찬 상 가장 작은 순서(즉, 오름 차순으로 된 순서)가 발견되면 false를 반환하여 순열을 구하는 반복문을 빠져나갈 수 있다.

## 코드 분석

![stl_next permutation](https://user-images.githubusercontent.com/34755287/48662100-2c245880-eac0-11e8-9216-ad1b3790e89a.jpg)

위 코드는 algorithm 헤더파일에 있는 ```next_permutation``` 함수와 참고자료를 참고하여 만들었으며, int 배열에서만 동작한다. ```next_permutation``` 함수는 배열의 뒤에서 부터 탐색하여 사전 편찬 방식에서 그 다음 순열을 찾는다.

시간 복잡도는 (last - first) / 2 swap(```reverse``` 함수의 시간 복잡도) * N(배열의 길이)이다. reference의 설명을 참고하면 O(N)으로 선형적이라고 설명하고 있다.

## 요구 사항
- 헤더: <algorithm>
- 네임스페이스: std

## 예제 코드

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

int main(void)
{
	int arr[4] = { 1, 2, 3, 4 };

	do {
		for (int i = 0; i < 4; ++i)
			printf("%d ", arr[i]);
		printf("\n");
	} while (next_permutation(arr, arr + 4));

	return 0;
}
```

```
1 2 3 4
1 2 4 3
1 3 2 4
1 3 4 2
1 4 2 3
1 4 3 2
2 1 3 4
2 1 4 3
2 3 1 4
2 3 4 1
2 4 1 3
2 4 3 1
3 1 2 4
3 1 4 2
3 2 1 4
3 2 4 1
3 4 1 2
3 4 2 1
4 1 2 3
4 1 3 2
4 2 1 3
4 2 3 1
4 3 1 2
4 3 2 1
```

## 참고 자료
- [Microsoft MSDN next_permutation](https://msdn.microsoft.com/ko-kr/library/e7d3xas6.aspx)
- [http://jeonggyun.tistory.com/110](http://jeonggyun.tistory.com/110)
