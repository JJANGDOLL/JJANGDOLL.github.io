---
layout: default
title: "삼각 함수"
nav_order: 2
parent: "[Math] 게임 기초 수학"
use_math: true
---

# 삼각 함수
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 제 1 코사인 법칙

각 점을 A,B,C 라고 하고, 그 점을 마주보는 선분을 a,b,c 라고 가정한다.

<img src="/images/trigon/trigonometric_first_cos_rule.png">

일 때

$a = b \cos C + c \cos B$

$b = c \cos A + a \cos C$

$c = a \cos B + b \cos A$

이다.

&nbsp;<br/>


## 제 1 코사인 법칙의 증명

* $a = b \cos C + c \cos B$ 에 대한 증명

<img src="/images/trigon/trigonometric_first_cos_rule_proof.png">

꼭지점 A에서 a를 향하여 수선의 발을 내린다.

$ a_{1} = c \cos B = c \cdot \frac{a_{1}}{c} = a_{1}$

$ a_{2} = b \cos C = b \cdot \frac{a_{2}}{b} = a_{2}$

$\therefore a = a_{1} + a_{2} = b \cos C + c \cos B$

&nbsp;<br/>
&nbsp;<br/>

# 제 2 코사인 법칙

각 점을 A,B,C 라고 하고, 그 점을 마주보는 선분을 a,b,c 라고 가정한다.

<img src="/images/trigon/trigonometric_first_cos_rule.png">

일 때,

$a^2 = b^2 + c^2 - 2bc \cos A$

$b^2 = a^2 + c^2 - 2ac \cos B$

$c^2 = a^2 + b^2 - 2ab \cos C$

이다.

&nbsp;<br/>

## 제 2 코사인 법칙 증명

제 1코사인 법칙에서 각 식을 모두 제곱해준다.

$a^2 = ab \cos C + ac \cos B$

$b^2 = bc \cos A + ba \cos C$

$c^2 = ca \cos B + cb \cos A$

그러면 $a^2$ 의 식을 아래처럼 변환할 수 있다.

$a^2 = ab \cos C + ac \cos B$

$ = b^2 - bc \cos A + c^2 - cb \cos A$

$ = b^2 + c^2 - 2bc \cos A$

$\therefore a^2 = b^2 + c^2 - 2bc \cos A$

