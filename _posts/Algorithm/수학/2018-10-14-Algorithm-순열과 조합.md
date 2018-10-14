---
title: "[Algorithm]순열과 조합"
excerpt: "C++로 구현하는 순열과 조합"
search: true
categories:
  - Algorithm
tags:
  - 수학
toc: true
---

# 순열과 조합

## 경우의 수
- 합의 법칙: 분류
  - 분류의 조건
  1. 동시에 일어나지 않음.
  2. 하나도 빠짐없이 포함되어 있어야함.
- 곱의 법칙: 동시에 일어나는 일 = 함께 일어나는 일
- 예제

```
자연수 999이하의 수 중에서 숫자 5가 포함된 수의 개수를 구하라.
1. 합의 법칙 = 분류
1) 5가 1개 있는 경우의 수 (곱의 법칙)
- 5xx = 9 * 9 = 81 * 1
- x5x = 9 * 9 = 81 * 1
- xx5 = 9 * 9 = 81 * 1
2) 5가 2개 있는 경우의 수 (곱의 법칙)
- 55x = 9 * 2 = 18
- 5x5 = 9 * 2 = 18
- x55 = 9 * 2 = 18
3) 5가 3개 있는 경우의 수 (곱의 법칙)
- 555 = 1 * 3
=> (81 + 81 + 81) + (18 + 18 + 18) + 3 = 300
```

## 순열(Permutation)
- 순서 있는 나열
- ```nPr```: n개 중에서 r개를 뽑아서 일렬로 나열하는 경우의 수
- ```nPr = n! / (n-r)!```

### STL함수 next_permutation을 활용한 순열 구현(C++)
- 기본적으로 next_permutation은 ```nPn```을 구하는 함수이다.
- ```nPr```을 next_permutation을 활용하여 구할려면 ```nPn```을 구한 상태에서 r크기까지만 저장한다.
  - 하지만, ```nPn```과 시간복잡도가 같다는 단점이 있다.

#### 구현 코드

```cpp
/*
순열은 초기화가 필요하다.
for (int i = 0; i < n; ++i)
		set.push_back(i);
*/
void permutationSTL(vector<int> set, int n, int r) {
	do {
		for (int i = 0; i < r; ++i)
			printf("%d ", set[i]);
		printf("\n");
	} while (next_permutation(set.begin(), set.end()));
}
```

### 재귀 함수를 활용한 순열 구현(C++)

```cpp
void swapSet(vector<int> &num, int a, int b) {
	int tmp = num[a];
	num[a] = num[b];
	num[b] = tmp;
}

void permutation(vector<int> set, int setSize, int n, int r) {
	//종료 조건
	if (setSize == r) {
		for (int i = 0; i < setSize; ++i)
			printf("%d ", set[i]);
		printf("\n");
		return;
	}
	//분기 조건
	for (int i = setSize; i < n; ++i) {
		swapSet(set, i, setSize);
		permutation(set, setSize + 1, n, r);
		swapSet(set, i, setSize);
	}
}
```

## 중복 순열(Repeated permutation)
- 중복 원소를 포함하는 순서있는 나열

```cpp
void repeatedPerm(vector<int> set, int setSize, int n, int r) {
	//종료 조건
	if (setSize == r) {
		for (int i = 0; i < setSize; ++i)
			printf("%d ", set[i]);
		printf("\n");
		return;
	}
	//분기 조건
	for (int i = 0; i < n; ++i) {
		set[setSize] = i;
		repeatedPerm(set, setSize + 1, n, r);
	}
}
```

## 조합(Combination)
- 순서를 고려하지 않고 선택하는 방법의 수
- ```nCr```: n개 중에서 r개를 순서에 상관없이 선택하는 방법의 수
- ``` nCr = nPr / r! = n! / (n-r)!r! ```

### STL함수 next_permutation을 활용한 조합 구현(C++)
- n 크기의 배열에서 r 개수만큼의 1을 배열 뒤에서 부터 삽입하고 나머지는 0으로 채운다.
- 위의 배열을 이용하여 next_permutation 함수를 사용하여 순열을 구한 후 1에 해당하는 인덱스를 뽑는다.

#### 구현 코드

```cpp
void combinationSTL(vector<int> set, int n, int r) {
	for (int j = 0; j < n - r; ++j)
		set.push_back(0);
	for (int j = 0; j < r; ++j)
		set.push_back(1);
	do {
		for (int j = 0; j < n; ++j)
			if (set[j])
				printf("%d ", j);
		printf("\n");
	} while (next_permutation(set.begin(), set.end()));
}
```

### 재귀 함수를 활용한 조합 구현(C++)

```cpp
//set = vector<int>(n);
void repeatedComb(vector<int> set, int setSize, int n, int r, int idx) {
	//종료 조건
	if (setSize == r) {
		for (int i = 0; i < setSize; ++i)
			printf("%d ", set[i]);
		printf("\n");
		return;
	}
	if (n == idx) return;
	//분기 조건
	set[setSize] = idx;
	repeatedComb(set, setSize + 1, n, r, idx);  //idx 중복 가능
	repeatedComb(set, setSize, n, r, idx + 1);
}

```

## 중복 조합(Repeated combination)
- 중복 원소를 포함하여 선택하는 방법의 수

```cpp
void repeatedComb(vector<int> set, int setSize, int n, int r, int idx) {
	//종료 조건
	if (setSize == r) {
		for (int i = 0; i < setSize; ++i)
			printf("%d ", set[i]);
		printf("\n");
		return;
	}
	if (n == idx) return;
	//분기 조건
	set[setSize] = idx;
	repeatedComb(set, setSize + 1, n, r, idx);  //idx 중복 가능
	repeatedComb(set, setSize, n, r, idx + 1);
}
```

## 결과

### main 함수

```cpp
int main()
{
	int n = 3;
	int r = 2;

	vector<int> set;

	//초기화
	for (int i = 0; i < n; ++i)
		set.push_back(i);
	printf("permutationSTL(%d, %d)\n", n, r);
	permutationSTL(set, n, r);
	printf("============================\n");

	printf("permutation(%d, %d)\n", n, r);
	permutation(set, 0, n, r);
	printf("============================\n");

	printf("repeatedPerm(%d, %d)\n", n, r);
	repeatedPerm(set, 0, n, r);
	printf("============================\n");

	set.clear();
	printf("combinationSTL(%d, %d)\n", n, r);
	combinationSTL(set, n, r);
	printf("============================\n");

	set = vector<int>(n);
	printf("combination(%d, %d)\n", n, r);
	combination(set, 0, n, r, 0);
	printf("============================\n");

	printf("repeatedComb(%d, %d)\n", n, r);
	repeatedComb(set, 0, n, r, 0);

	return 0;
}
```

### 결과 화면

```
permutationSTL(3, 2)
0 1
0 2
1 0
1 2
2 0
2 1
============================
permutation(3, 2)
0 1
0 2
1 0
1 2
2 1
2 0
============================
repeatedPerm(3, 2)
0 0
0 1
0 2
1 0
1 1
1 2
2 0
2 1
2 2
============================
combinationSTL(3, 2)
1 2
0 2
0 1
============================
combination(3, 2)
0 1
0 2
1 2
============================
repeatedComb(3, 2)
0 0
0 1
0 2
1 1
1 2
2 2
```
