---
title: "[DataStructure]N^2 정렬 알고리즘"
excerpt: "버블 정렬, 선택 정렬, 삽입 정렬"
search: true
categories:
  - DataStructure
tags:
  - 정렬
toc: true
---

# O(N^2) 정렬 알고리즘

## 버블 정렬(Bubble Sort)
- 버블 정렬은 인접한 두 원소를 검사하여 정렬하는 방법이다.
- 버블 정렬 알고리즘 순서(오름차순)
  1. 주어진 배열 범위 중에서 첫 번째 위치와 두 번재 위치 크기를 비교하여 크기가 큰 수를 두 번째 위치에 둔다.
  2. 첫 번째 위치를 한 칸 뒤로 옮겨 과정 1을 반복한다.
    - 두 번째 위치가 주어진 배열 범위의 끝에 도달하면 마지막 위치를 제외하여 배열 범위를 설정하고 과정 1로 돌아간다.
    (첫 번째 과정이 끝나면 가장 큰 원소가 가장 끝에 위치한다.)
- 총 비교 횟수: ```(n-1) + (n-2) + (n-3) ... + 1```

- ![bubble sort](https://user-images.githubusercontent.com/34755287/46913139-cb869500-cfc2-11e8-8636-19d7e306c711.gif)

- 구현 코드(C언어)

```cpp
//오름차순
//arr[]: 입력 배열, n: arr배열 크기
void BubbleSort(int arr[], int n) {
	int i, j;
	int temp;

	for(i = 0; i < n - 1; i++)
		for(j = 0; j < (n - i) - 1; j++)
			if(arr[j] > arr[j + 1]) {
				temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;
			}
}
```

## 선택 정렬(Selection Sort)
- 선택 정렬 알고리즘 순서(오름차순)
  1. 주어진 배열 범위 중에서 최소값을 찾는다.
  2. 해당 값을 가장 앞에 위치한 값과 교환한다.
  3. 가장 앞에 위치한 값을 제외하여 배열 범위를 설정하고 과정 1로 돌아간다.

- ![selection sort](https://user-images.githubusercontent.com/34755287/46913144-ccb7c200-cfc2-11e8-89f4-3b6db8bd339a.gif)

- 구현 코드(C언어)

```cpp
//오름차순
//arr[]: 입력 배열, n: arr배열 크기
void SelectionSort(int arr[], int n) {
	int i, j;
	int maxIdx;
	int temp;

	for(i = 0; i < n - 1; i++) {
		maxIdx = i;    // 정렬 순서상 가장 앞서는 데이터의 index

    // 최소값 탐색
		for(j = i + 1; j < n; j++) {
			if(arr[j] < arr[maxIdx])
				maxIdx = j;
		}

		/* 교환 */
		temp = arr[i];
		arr[i] = arr[maxIdx];
		arr[maxIdx] = temp;
	}
}
```

## 삽입 정렬(Insertion Sort)
- 삽입 정렬 알고리즘 순서(오름차순)
  0. 배열의 두 번째 위치부터 시작한다.
  1. 현재 위치보다 앞선 위치를 차례대로 순회하며 현재 값보다 작은 값을 찾는다.
  2. 작은 값을 찾으면, 그 바로 다음 위치로 옮기고 현재 위치 바로 다음 위치에서 과정 1을 반복한다.

- ![insertion sort](https://user-images.githubusercontent.com/34755287/46913140-cb869500-cfc2-11e8-81cf-e1fdfceec963.gif)

- 구현 코드(C언어)

```cpp
//오름차순
//arr[]: 입력 배열, n: arr배열 크기
void InserSort(int arr[], int n) {
	int i, j;
	int insData;

  //두 번째 위치부터 시작
	for(i = 1; i < n; i++) {
		insData = arr[i];          // 정렬 대상을 insData에 저장

		for(j = i - 1; j >= 0 ; j--) {
			if(arr[j] > insData)
				arr[j + 1] = arr[j];   // 비교 대상 한 칸 뒤로 밀기
			else
				break;                 // 삽입 위치 찾았으니 탈출!
		}

		arr[j + 1] = insData;        // 찾은 위치에 정렬 대상 삽입!
	}
}
```
