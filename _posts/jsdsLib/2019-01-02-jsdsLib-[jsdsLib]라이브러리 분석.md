---
title: "[jsdsLib]라이브러리 분석"
excerpt: "jsdsLib 내부 구조, 성능, 등 분석"
categories:
  - Toy
tags:
  - Toy_jsdsLib
search: true
sidebar_main: true
toc: true
---

## List
이 라이브러리의 리스트는 doubly linked list 이다. 구현의 편의성을 위해서 head 와 tail 부분에 dummy 노드를 가르키게 하였다.

![list structure](https://user-images.githubusercontent.com/34755287/50594895-b9d9cd00-0ee1-11e9-9ad1-812c0b35e01d.JPG)

## Stack
일반적인 배열 스택이다. 배열의 맨 뒤의 데이터가 삽입, 삭제되므로 시작복잡도는 O(1) 을 보장한다.

## Queue
배열로 만들어진 큐이다. 큐는 맨 앞의 데이터가 삭제되므로, 일반적인 자바스크립트에서 ```.shift()``` 매서드를 사용하면 O(N) 으로 성능이 좋지 않다. 맨 앞의 데이터를 삭제하고 나머지 데이터들을 모두 앞으로 한 칸씩 당겨야하기 때문이다. 이를 개선하기 위해서 맨 앞의 데이터를 삭제하지만, 매번 나머지 데이터들을 앞으로 당기지 않도록 한다. 먼저, 코드를 보자.

```js
var dequeueData = this._dataArray[this._frontIdx++];
if(this._frontIdx * 1.5 > this._dataArray.length) {
  this._dataArray = this._dataArray.slice(this._frontIdx);
  this._frontIdx = 0;
}
return dequeueData;
```

위는 실제 라이브러리 ```dequeue()``` 함수 부분이다. ```_frontIdx``` 변수는 배열의 맨 앞의 데이터를 가르킨다. 맨 앞의 데이터를 삭제하고 이 변수를 한 칸 앞으로 옮겨준다. 이러한 수행에서 시간복잡도는 O(1) 이다. 하지만 삽입, 삭제 연산이 늘어날수록 배열의 크기도 계속 늘어나는 단점이 있다. 이를 보완하기 위해 삭제된 데이터 공간이 일정량 쌓이면, 배열 전체를 인덱스가 0인 기준으로 재배치해준다.

## Deque
Deque(덱, 데크) 는 스택과 큐를 합친 자료구조이다. 여기에 더해 맨 앞에서도 데이터를 추가할 수 있다. 맨 앞의 데이터를 삭제하는 연산을 ```.shift()``` 매서드를 쓰지 않고 좀 더 성능을 개선한 방법은 위의 큐에서 설명하였다. 하지만 맨 앞에 데이터를 추가하는 것 역시 일반적인 ```.unshift()``` 매서드를 사용한다면 성능이 좋지않다. 이를 개선하기 위해 npm 패키지 중 deque 를 구현한 코드를 참고하였다. 아직까지 완전한 이해를 하지 못하여 추후에 좀 더 분석하여 올릴 예정이다.
- 참고 코드: <https://github.com/petkaantonov/deque/blob/master/src/deque.js>

## Priority Queue
우선 순위 큐는 일반적인 heap 를 이용하여 구현하였다. 조금의 성능 개선을 위해 데이터를 삽입 또는 삭제한 후의 다시 heap 배열의 우선 순위를 맞추는 연산에서 실제 데이터 사이 교환(swap)을 하지 않고 인덱스만 저장한다. 그리고 최종 인덱스에 데이터를 저장하는 방식으로 구현되어 있다.

## 성능 분석

## 자바스크립트
- 특징
- 한계
