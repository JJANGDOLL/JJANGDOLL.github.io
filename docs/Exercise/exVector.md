---
layout: default
title: "Vector Exercise"
nav_order: 1
parent: "Exercise"
use_math: true
---

# 벡터 연습문제
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 1. 2차원 벡터 연산

**$ u = ( 1, 2)$ 이고 $v = (3, -4)$ 라고 하자. 아래 계산을 수행하고, 벡터들을 2차원 좌표계를 기준으로 그려보아라**

&nbsp; <br/>


## $ u+v $

<img src="/images/exercise/1_a.png">

&nbsp;<br/>

## $ u - v$

<img src="/images/exercise/1_b.png">

&nbsp;<br/>

## $2u + \frac{1}{2}v$

<img src="/images/exercise/1_c.png">

&nbsp;<br/>

## $-2u + v $

<img src="/images/exercise/1_d.png">

&nbsp;<br/>

# 2. 3차원 벡터 연산

**$u=(-1, 3, 2)$ 이고 $v = (3, -4, 1)$ 이라고 하자. 다음 계산을 수행하라.**

&nbsp; <br/>


## $u + v$

$u + v $

$ = (-1, 3, 2) + (3, -4 ,1)$

$ = (-1 + 3, 3 + -4, 2 + 1)$

$ = (2, -1, 3)$

&nbsp;<br/>

## $u - v$

$ u - v$

$ = (-1, 3, 2) - (3, -4, 1)$

$ = (-1 - 3, 3 - -4, 2 - 1)$

$ = (-4, 7, 1)$

&nbsp;<br/>

## $3u + 2v$

$ 3u + 2v$

$ = 3(-1, 3, 2) + 2(3, -4, 1)$

$ = (-3,9, 6) + (6, -8, 2)$

$ = (-3 + 6, 9 + -8, 6 + 2)$

$ = (-11, 1, 8)$

&nbsp; <br/>

## $-2u + v$

$ -2u + v$

$ = -2(-1, 3, 2) + (3, -4, 1)$

$ = (2, -6, -4) + (3, -4, 1)$

$ = (2 + 3, -6 + -4, -4 + 1)$

$ = (5, -10, -3)$

&nbsp; <br/>


# 3. 벡터 대수의 다양한 법칙 증명

**백터 대수에도 실수의 여러 좋은 법칙들이 성립한다.**

$u = (u_x, u_y, u_z), v = (v_x, v_y, v_z), w = (w_x, w_y, w_z), c, k = \{x \| x \in \mathbb{R} \} $ 일 때,

아래 법칙을 증명하라.

## $u + v = v + u$

$u + v$

$ = (u_x, u_y, u_z) + (v_x, v_y,v_z)$

$ = (u_x + v_x , u_y + v_y, u_z+v_z)$

$ = (v_x + u_x, v_y + u_y, v_z+u_z)$

$= v + u$

&nbsp; <br/>

## $ u + ( v + w) = (u+v) + w$

$ u + ( v + w)$

$ = (u_x, u_y ,u_z) + (v_x + w_x, v_y + w_y, v_z + w_z)$

$ = (u_x + v_x + w_x, u_y + v_y + w_y, u_z + v_z + w_z)$

$ = (u_x + v_x, u_y + v_y, u_z + v_z) + (w_x, w_y, w_z)$

$ = (u+v) + w$

&nbsp;<br/>

## $(ck)u = c(ku)$

$(ck)u$

$ = ( (ck)u_x, (ck)u_y, (ck)u_z)$

$ = ( c(ku_x), c(ku_y), c(ku_z))$

$ = c(ku_x, ku_y, ku_z)$

$ = c(ku)$

&nbsp;<br/>

## $k(u + v) = ku + kv$

$k(u + v)$

$ = k(u_x + v_x, u_y + v_y, u_z + v_z)$

$ = ( k(u_x + v_x), k(u_y + v_y) , k(u_z + v_z))$

$ = (ku_x + kv_x, ku_y + kv_y, ku_z + kv_z)$

$ = (ku_x, ku_y, ku_z) + (kv_x, kv_y, kv_z)$

$ = k(u_x, u_y, u_z) = k(v_x, v_y, v_z)$

$ = ku + kv$

&nbsp;<br/>

## $u(k+c) = ku + cu$

$u(k+c)$

$= ((k+c) u_x, (k+c)u_y, (k+c)u_z)$

$ = (ku_x + cu_x, ku_y + cu_y, ku_z + cu_z)$

$ = (ku_x, ku_y, ku_z) + (cu_x, cu_y, cu_z)$

$ = ku + cu$

&nbsp;<br/>

# 벡터 $x$의 값 찾기

$2((1,2,3)-x) - (-2, 0, 4) = -2(1, 2, 3)$ 을 만족하는 $x$ 를 구하라.

**풀이**

$2((1, 2, 3)-x) - (-2, 0, 4)$

$ = (2, 4, 6) - 2x - (-2, 0, 4)$

$ = (2-(-2), 4, 6-4) - 2x$

$ = (4, 4, 2) - 2x = -2(1, 2, 3)$

$= -2x = -2(1, 2, 3) - (4, 4, 2)$

&nbsp; <br/>

$-2x = -2(1, 2, 3) - (4, 4, 2)$

$x = (1, 2, 3) - \frac{(4, 4, 2)}{-2}$

$= (1, 2, 3) - (-2, -2, -1)$

$= (3, 4, 4)$

$\therefore x = (3, 4, 4)$

&nbsp; <br/>

# 벡터 정규화

$ u = (-1, 3, 2), v = (3, -4, 1)$ 일 때, u 와 v를 정규화하라

$\Vert \vec u \Vert = \sqrt{-1^2 + 3^2 + 2^2} = \sqrt{14}$

$\hat{u} = (\frac{-1}{\sqrt{14}}, \frac{3}{\sqrt{14}},\frac{2}{\sqrt{14}})$ 

$\Vert \vec v \Vert = \sqrt{3^2 + -4^2 + 1^2} = \sqrt{26}$

$\hat{v} = (\frac{3}{\sqrt{26}}, \frac{-4}{\sqrt{26}},\frac{1}{\sqrt{26}})$

&nbsp; <br/>
