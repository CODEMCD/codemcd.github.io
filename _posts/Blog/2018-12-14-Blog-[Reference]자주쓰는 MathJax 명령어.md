---
title: "[Reference]자주쓰는 MathJax 문법"
excerpt: "블로그에 수학식 사용할 때 쓰는 MathJax 문법 정리"
categories:
  - Toy
tags:
  - Toy_Blog
search: true
sidebar_main: true
use_math: true
toc: true
---

블로그 post를 작성할 때, 수학식을 사용해야할 경우가 있었다. 그래서 구글링을 해본 결과 jekyll에서 MathJax를 사용하는 방법이 있었다.

## MathJax란?
Mathjax는 MathML, LaTeX 및 ASCIIMAthML 마크 업을 사용하여 웹 브라우저에 수학 표기법을 표시하는 크로스 브라우저 JavaScript 라이브러리이다. 그리고 Apache 라이센스에 따라 오픈 소스 소프트웨어이다. (출처: 위키백과)

## MathJax 사용법
먼저, post에서는 front matter에 ```use_math: true```를 추가하고 MathJax 양식대로 쓰면 자동으로 렌더링하여 표시해준다.

MathJax 사용법은 2가지가 있는데, ```$```나 ```$$```를 수식 앞뒤에 선언해주어야 한다. ```$``` 는 수학식을 문장 사이에 넣을 때 사용하고, ```$$``` 는 수학식으로 해당 줄을 모두 채우고 싶은 경우에 사용한다.

## 자주쓰는 수식
자주 사용하는 수학식을 만드는 MathJax를 모아보았다. ```수식 => 결과``` 로 표현하였다. 수식을 복사하여 위에 설명한 MathJax 사용법대로 선언하여 사용할 수 있다.

### 사칙 연산
- ```A + B = C``` => $A + B = C$
- ```A - B = C``` => $A - B = C$
- ```A * B = C``` => $A * B = C$
- ```A \times B = C``` => $A \times B = C$
- ```A / B = C``` => $A / B = C$
- ```A \div B = C``` => $A \div B = C$

### 지수 표현
- ```e^x``` => $e^x$
- ```a_i``` => $a_i$
- ```a_i-1``` => $a_i-1$
- ```a_{i-1}``` => $a_{i-1}$
- ```x^2 + 2x + 1``` => $x^2 + 2x + 1$
- ```a_i^2``` => $a_i^2$
- ```a_{i-1}^2x+3``` => $a_{i-1}^2x+3$
- ```a_{i-1}^{2x+3}``` => $a_{i-1}^{2x+3}$
- ```{a^b}^c``` => ${a^b}^c$

### 부등식
- ```A < B``` => $A < B$
- ```A \le B``` => $A \le B$
- ```A > B``` => $A > B$
- ```A \ge B``` => $A \ge B$
- ```A = B``` => $A = B$
- ```A \ne B``` => $A \ne B$

### ...
- ```A_1, A_2, \ldots, A_{N-1}, A_N``` => $A_1, A_2, \ldots, A_{N-1}, A_N$
- ```A_1 + A_2 + \cdots + A_{N-1} + A_N``` => $A_1 + A_2 + \cdots + A_{N-1} + A_N$

### Root
- ```\sqrt{2}``` => $\sqrt{2}$
- ```\sqrt{x^2}``` => $\sqrt{x^2}$
- ```\sqrt[3]{x^2}``` => $\sqrt[3]{x^2}$

### 분수
- ```\dfrac``` 은 큰 분수(display), ```\cfrac``` 은 연속 분수(continued)
- ```\frac{A}{B}``` => $\frac{A}{B}$
- ```\frac{2}{3} + \frac{3}{4}``` => $\frac{2}{3} + \frac{3}{4}$
- ```\frac{dy}{dx}``` => $\frac{dy}{dx}$
- ```\dfrac{-b \pm \sqrt{b^2 - 4ac}}{2a}``` => $\dfrac{-b \pm \sqrt{b^2 - 4ac}}{2a}$
- ```x = a_0 + \cfrac{1}{a_1 + \cfrac{1}{a_2 + \cfrac{1}{a_3}}}``` => $x = a_0 + \cfrac{1}{a_1 + \cfrac{1}{a_2 + \cfrac{1}{a_3}}}$

### 괄호
- 괄호 안에 들어가는 수식과 괄호의 길이를 맞춰주려면, 여는 괄호 앞에 ```\left```, 닫는 괄호 앞에 ```\right``` 를 선언해야한다.
- ```\left( \frac{ \frac{A}{B} }{ \frac{C}{D} } \right)``` => $\left( \frac{ \frac{A}{B} }{ \frac{C}{D} } \right)$
- ```\lfloor \frac{x}{2} \rfloor \left\lfloor \frac{x}{2} \right\rfloor``` => $\lfloor \frac{x}{2} \rfloor \left\lfloor \frac{x}{2} \right\rfloor$
- ```\lceil \frac{x}{2} \rceil \left\lceil \frac{x}{2} \right\rceil``` => $\lceil \frac{x}{2} \rceil \left\lceil \frac{x}{2} \right\rceil$
- ```|x| \|x\|``` => $|x| \|x\|$

### 시그마
- ```\sum {x}``` => $\sum {x}$
- ```\sum_1^n {x}``` => $\sum_1^n {x}$
- ```\sum_{i=1}^{\infty} {i^2}``` => $\sum_{i=1}^{\infty} {i^2}$
- ```\prod \coprod \bigcup \bigcap \bigvee \bigwedge``` => $\prod \coprod \bigcup \bigcap \bigvee \bigwedge$

### 인테그럴
- ```\int_{a}^{b}{f(x)dx}``` => $\int_{a}^{b}{f(x)dx}$

### 삼각함수
- ```sin(x)``` => $sin(x)$
- ```\sin^{2}{x}``` => $\sin^{2}{x}$

### 로그(Log)
- ```\log{N}``` => $\log{N}$
- ```\log_2{N}``` => $\log_2{N}$

### 집합 관계
- ```A \cap B``` => $A \cap B$
- ```A \cup B``` => $A \cup B$

### 행렬
- ```\begin{matrix} 1 & 2 \\ 3 & 4 \end{matrix}``` => $\begin{matrix} 1 & 2 \\ 3 & 4 \end{matrix}$
- ```\begin{pmatrix} 1 & 2 \\ 3 & 4 \end{pmatrix}``` => $\begin{pmatrix} 1 & 2 \\ 3 & 4 \end{pmatrix}$
- ```\begin{bmatrix} 1 & 2 \\ 3 & 4 \end{bmatrix}``` => $\begin{bmatrix} 1 & 2 \\ 3 & 4 \end{bmatrix}$

### 기타
- ```\lim_{x \to 0} {x^2}``` => $\lim_{x \to 0} {x^2}$
- ```A \equiv B \pmod n``` => $A \equiv B \pmod n$
- ```\mathbf{알파벳}``` => $\mathbf{A}$
