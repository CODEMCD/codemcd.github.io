---
title: "Jekyll Blog"
layout: archive
permalink: /tags/toy-blog
author_profile: true
sidebar_main: true
---

- [POST 바로가기](#posts)

## Blog Spec
- Github + Jekyll
- Theme: Minimal Mistakes
- 댓글 기능: disqus
- 검색 기능: algolia
- CI(Continuous Integration) Tool: Travis CI


## POST 구조

![post structure](https://user-images.githubusercontent.com/34755287/50006268-872e6a00-fff0-11e8-84d3-cc3f52f0815d.JPG)

가장 큰 주제를 카테고리(category)로 나누고 그 밑의 소주제를 테그(tag)로 분류하였다. 기존의 카테고리, 테그 구조를 활용하는 것이 가장 쉽게 구현할 수 있을 것이라 예상했다. 실제로 간단히 liquid 템플릿 코드만 조금 수정해서 위의 구조와 같이 만들 수 있었다.


## 참고 자료
### 공식 사이트
- [Jekyll](https://jekyllrb-ko.github.io/)
- [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/)
- [Liquid](https://shopify.github.io/liquid/)
### 블로그
- [Minimal mistakes 테마 적용](https://junhobaik.github.io/jekyll-apply-theme/)
- [Travis CI, algolia 등 적용](https://imreplay.com/blogging/minimal-mistakes-%ED%85%8C%EB%A7%88%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%B4-githubio-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0/)
- [Jekyll 분석](http://jihyeleee.com/blog/third-designer-can-make-jekyll-blog/)


---
# POSTS

{% assign posts = site.tags.Toy_Blog | sort:"date" | reverse  %}

{% for post in posts %}
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}
