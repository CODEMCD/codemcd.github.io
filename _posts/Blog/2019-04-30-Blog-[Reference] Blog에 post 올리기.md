---
title: "[Reference] Blog에 post 올리기"
excerpt: "최신 업데이트 날짜: 2019-04-30"
date: 2019-04-30 14:00:00
categories:
  - Toy
tags:
  - Toy_Blog
search: true
sidebar_main: true
toc: true
---

>> 이 자료는 해당 블로그 전용입니다. 다른 블로그와 설정이 다를 수 있씁니다.

# POST
Jekyll 블로그에서 post란 일반적으로 블로그에서 쓰는 글이다. 하지만 jekyll에서는 post가 마크다운(Markdown) 으로 작성되고, 기본적으로 몇 가지 규칙이 있다.
- 모든 post는 ```_posts``` 파일안에 있어야 한다.
- 모든 post의 파일 이름은 ```YYYY-MM-DD-TITLE.md``` 양식을 따라야한다. 예를 들어 ```2018-07-15-test.md```로 쓸 수 있다.


# Front matter
Front matter(머리말) 은 jekyll이 마크다운을 해석할 때, 사용하는 메타 정보이다. 이는 반드시 아래와 같이 2가지를 지켜서 사용해야 한다.
- post 가장 맨 첫 부분에 위치해야한다.
- 시작과 끝을 3개의 대쉬(---)로 감싸주어야 한다.
Front matter 은 YAML 형식이고, 비워도 상관없다.

## Front matter 설정

```yml
title: "POST 제목"
excerpt: "POST 설명"
date: YYYY-MM-DD hh:mm:ss
categories:
  - category1
tags:
  - tag1
search: true
sidebar_main: true
use_math: true
toc: true
```

Front matter 의 예시는 위와 같다. 현재 사용중인 옵션을 모두 보여준 것이다.
- title: POST의 제목
- excerpt: POST의 간단한 설명 (현재는 최신 업데이트 날짜로 통일하였다.)
- date: 포스트를 작성 및 업데이트한 날짜
- categories: 카테고리를 설정 (여러 개를 적을 수도 있다.)
- tags: 테그를 설정
- search: 현재 POST를 검색이 가능하도록 할지 설정하는 부분이다. true 값을 넣으면 해당 POST를 검색할 수 있다.
- sidebar_main: 현재 POST에서 사이드바를 표시할지 설정하는 부분이다.
- use_math: 현재 POST에서 MathJax 기능을 사용할지 설정하는 부분이다.
- toc: POST 맨 첫 부분에 해당 POST의 각 header('#', '##', ...)에 바로 가는 기능을 사용할지 설정하는 부분이다.


# Category와 Tag
현재 블로그 post를 원하는 방식으로 분류하기 위해 카테고리와 테그 설정을 고정적으로 설정하였다. 먼저, 카테고리는 3개로 정하였다.
- Toy: 토이 프로젝트
- Algorithm: 알고리즘, PS
- Study: 각종 프로그래밍 관련 공부

위와 같이, 카테고리는 3개만 가능하고 테그 양식은 ```Category_Tag``` 로 사용해야 한다. 예를 들어, 토이 프로젝트의 블로그 개발 관련 post를 작성하였다면 카테고리는 ```Toy```, 테그는 ```Toy_Blog``` 와 같이 작성하여야 한다. 실제 블로그에서 테그는 앞의 카테고리는 나타나지 않게 설정하였다. 즉, "Blog" 라는 글자만 볼 수 있다.
