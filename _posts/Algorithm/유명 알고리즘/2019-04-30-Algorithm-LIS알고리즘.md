---
title: "LIS(Longest Increasing Subsequence) 알고리즘"
excerpt: "최신 업데이트 날짜: 2019-04-30"
date: 2019-04-30 15:45:00
categories:
  - Algorithm
tags:
  - Algorithm_Algorithm
search: true
sidebar_main: true
toc: true
---

LIS 알고리즘은 **가장 긴 증가하는(최장증가) 부분 수열** 이다. 아래의 그림 처럼 부분 수열이므로 연속적이지 않아도 된다. (2번 째 배열의 노란색 박스가 LIS이다.)

![lis](https://user-images.githubusercontent.com/34755287/46916274-410a5980-cff3-11e8-948a-31488df44de1.JPG)

LIS 알고리즘을 해결하는 방법은 시간복잡도가 O(N^2)과 O(NlogN) 두 가지 방법이 대표적이다.

## 해결 방법1: 동적 계획법
동적 계획법을 이용한 해결 방법은 시간복잡도 O(N^2)에 속한다. 동적 계획법에서도 Bottom-up 방식과 Top-down 방식 두 가지 모두 알아보자.

```
DP[i] = i 번째 위치일 때, 최장 증가 부분 수열의 개수 (cache 배열도 동일)
```

### Bottom-up
Bottom-up은 반복문을 사용하는 동적 계획법이다. 현재 위치 이전의 값들을 차례대로 순회하면서 현재 위치에서 최장증가 부분 수열의 개수를 갱신한다. 이는 말보다 코드로 이해하는 것이 쉽다.

```cpp
// num[]: 숫자 배열
// n: num 배열의 크기
// lis: 가장 긴 증가하는 부분 수열의 개수 (0으로 초기화)
DP[0] = 1;
for (int i = 1; i < n; ++i) {
  dp[i] = 1;
  // 0 ~ i - 1 위치까지 반복
  for (int j = 0; j < i; ++j) {
    // 1) 현재 위치의 값보다 작고,
    // 2) 최장증가부분 수열 개수가 1개 증가했을 때, 현재 가지고 있는 수열 개수보다 크다면
    if(num[i] > num[j] && dp[i] < dp[j] + 1) {
      dp[i] = dp[j] + 1;
    }
  }

  lis = max(lis, dp[i]);
}
```

### Top-down
Top-down은 재귀를 사용한 메모이제이션 방식이다. 이 방식은 대부분 완전탐색에서 최적화한 모습이므로 완전탬색 코드와 유사한 로직을 가진다. 여기서는 전체 배열을 탐색하는 구현을 간단히 하기 위해 **-1** 부터 시작한다.

```cpp
// cache[]: -1으로 초기화
// 가장 긴 증가하는 부분 수열의 개수 반환(start 초기값은 -1)
int Memoization(int start)
{
  int& ret = cache[start + 1];
  if (ret != -1) return ret;

  ret = 0;
  for (int next = start + 1; next < n; ++next) {
    if (start == -1 || num[start] < num[next]) {
      int candidate = Memoization(next) + 1;
      if (candidate > ret)
        ret = candidate;
    }
  }

  return ret;
}
```


## 해결 방법2: 이분 탐색
LIS는 이분 탐색을 사용하여 시간복잡도 O(NlogN)으로 해결할 수 있다. 이는 **lower_bound** 를 사용하는 것으로, lower_bound는 찾으려는 값과 같으면 그 위치를 반환하고 없다면 이 값보다 큰 수 중 가장 작은 값의 위치를 반환한다. 이는 이분 탐색으로 찾기 때문에 O(logN)의 시간복잡도는 가지고 있다. 그러면 예제를 살펴보자.

```
10, 20, 10, 30, 20, 50
```

위 예제에서 처음 위치부터 끝까지 순회하면서 해당 숫자의 위치를 vector에 저장한다. 이를 계산한 표를 살펴보자.

| i/num[] | 10 | 20 | 10 | 30 | 20 | 50 |
|:-------:|:--:|:--:|:--:|:--:|:--:|:--:|
| 0 | 10 |  |  |  |  |  |
| 1 | 10 | 20 |  |  |  |  |
| 2 | 10 | 20 |  |  |  |  |
| 3 | 10 | 20 | 30 |  |  |  |
| 4 | 10 | 20 | 30 |  |  |  |
| 5 | 10 | 20 | 30 | 50 |  |  |

1. i = 0: 처음 위치는 바로 vector에 삽입한다.
2. i = 1: 숫자 20은 vector안에 존재하는 10보다 크므로 그 뒤에 삽입한다.
3. i = 2: 숫자 10은 vector안에 이미 존재하므로 이를 대체한다.(무시하는 것과 같다.)
4. i = 3: 숫자 30은 vector안에 존재하는 마지막 숫자 20보다 크므로 그 뒤에 삽입한다.
5. i = 4: 숫자 20은 vector안에 이미 존재하므로 이를 대체한다.
6. i = 5: 숫자 50은 vector안에 존재하는 마지막 숫자 30보다 크므로 그 뒤에 삽입한다.

위처럼 배열 크기만큼 순회하면서 그 때마다 해당 숫자의 위치를 lower_bound로 탐색하므로 시간복잡도 O(NlogN)을 보장한다.

```cpp
lis.push_back(num[0]);
vector<int>::iterator it;
for (int i = 1; i < n; ++i) {
		it = lower_bound(lis.begin(), lis.end(), num[i]);
		if (it == lis.end())
			lis.push_back(num[i]);
		else
			*it = num[i];
}
```
