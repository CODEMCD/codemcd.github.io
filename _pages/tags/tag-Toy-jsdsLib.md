---
title: "jsdsLib"
layout: archive
permalink: /tags/toy-jsdslib
author_profile: true
sidebar_main: true
---

## POSTS

{% assign posts = site.tags.Toy_jsdsLib | sort:"date" | reverse  %}

{% for post in posts %}
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}

---

## jsdsLib
- 자바스크립트로 구현된 자료구조 라이브러리
- 사용 가능 환경
  - HTML: CDN 서비스를 활용하여 head 부분에 라이브러리를 추가하여 사용할 수 있다.
  - npm: 해당 라이브러리를 npm 패키지로 배포한 상태이다. (```npm install jsdslib``` 명령어로 불러올 수 있다.)
- 자료구조 목록: List, Stack, Queue, Deque, Priority Queue
  - [자료구조 내부 구조](https://codemcd.github.io/toy/jsdsLib-jsdsLib-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-%EB%B6%84%EC%84%9D/)
- [jsdsLib 사용하기](https://github.com/CODEMCD/jsdsLib)

## Data Structures Visualization
- 자료구조의 데이터 상황을 시각적으로 표현하는 웹 서비스
- 자료구조 목록: Stack, Queue
- [Visualization 사용하기](https://github.com/CODEMCD/jsdsLib-Visualization)