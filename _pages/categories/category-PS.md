---
title: "PS"
layout: archive
permalink: /categories/ps
author_profile: true
sidebar_main: true
---

# PS(Problem Solve)
- 알고리즘 문제 해결 방법 및 코드 분석

## 알고리즘 문제 사이트
### [Baekjoon Online Judge(BOJ)](https://www.acmicpc.net/)

### [ALGOSPOT](https://algospot.com/)
- 알고리즘 문제 해결 전략(구종만)

### [SW Expert Academy](https://www.swexpertacademy.com/main/main.do)
- 삼성

## 그 외 알고리즘 문제 코드
- [https://github.com/CODEMCD/Algorithm/tree/master/CODE](https://github.com/CODEMCD/Algorithm/tree/master/CODE)

---

{% assign posts = site.categories.PS | sort:"date" %}

{% for post in posts %}
  {% include archive-single.html type=page.entries_layout %}
{% endfor %}
