---
layout: default
title: "Vector Exercise"
nav_order: 1
parent: "Exercise"
use_math: true
---

# 벡터 연습문제
{: .no_toc}

DirectX 12를 이용한 3D 게임 프로그래밍 입문 문제풀이

1장 벡터 대수

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 2차원 벡터 연산

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

# 3차원 벡터 연산

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


# 벡터 대수의 다양한 법칙 증명

**백터 대수에도 실수의 여러 좋은 법칙들이 성립한다.**

$u = (u_x, u_y, u_z), v = (v_x, v_y, v_z), w = (w_x, w_y, w_z), c, k = \{ x \vert x \in \mathbb{R} \} $ 일 때,

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

# 증명 문제

$k$ 는 스칼라, $u = (u_x, u_y, u_z)$ 일 때, $\Vert k \vec u \Vert = \vert k \vert \Vert \vec u \Vert $ 임을 증명하라.

**풀이**

$\Vert k \vec u \Vert$ 

$ = \Vert k (u_x, u_y, u_z) \Vert$

$ = \Vert (ku_x, ku_y, ku_z) \Vert$

$ = \sqrt{(ku_x)^2 + (ku_y)^2 + (ku_z)^2}$

$ = \sqrt{k^2}\cdot\sqrt{u_x^2 + u_y^2 + u_z^2}$

$ = \vert k \vert \cdot \Vert \vec u \Vert $

&nbsp; <br/>

# 내적 연습문제

$\vec u, \vec v$ 에 대해 둘 사이가 직각인지, 예각인지, 둔각인지 밝혀라

&nbsp; <br/>


## 1번 문제

$u = (1, 1, 1), v = (2, 3, 4)$

$ u \cdot v = 1 \cdot 2 + 1 \cdot 3 + 1 \cdot 4 = 9$


따라서, 예각이다.

&nbsp; <br/>


## 2번 문제

$u = (1, 1, 0), v = (-2, 2, 0)$

$ u \cdot v = 1 \cdot -2 + 1 \cdot 2 + 0 \cdot 0 = 0$

따라서, 직각이다.

&nbsp; <br/>

## 3번 문제

$u = (-1, -1, -1), v = (3, 1, 0)$

$ u \cdot v = -1 \cdot 3 + -1 \cdot 1 + -1 \cdot 0 = -4$

따라서, 둔각이다.

&nbsp; <br/>

# 사잇각 구하기

$u = (-1, 3, 2), v = (3, -4, 1)$ 일 때, $u$ 와 $v$의 사잇각 $\theta$ 를 구하여라.

**풀이**

$\Vert \vec u \Vert \cdot \Vert \vec v \Vert \cdot \cos{\theta} = -1 \cdot 3 + 3 \cdot -4 + 2 \cdot 1$

$\sqrt{-1^2 + 3^2 + 2^2} \cdot \sqrt{3^2 + -4^2 + 1^2} \cdot \cos{\theta} = -13$

$\cos{\theta} = \frac{-13}{\sqrt{14} \cdot \sqrt{26}} = \frac{-1 \cdot \sqrt{13}}{2 \cdot \sqrt{7} } = -\frac{\sqrt{13}}{2 \sqrt{7}}$

&nbsp; <br/>

# 내적의 성질 증명

$ u = (u_x, u_y, u_z), v = (v_x, v_y, v_z), w = (w_x, w_y, w_z)$ 일 때,

## $u \cdot  v =  v \cdot  u$

$u \cdot v$

$ = u_x \cdot v_x + u_y \cdot v_y + u_z \cdot v_z $

$ = v_x \cdot u_x + v_y \cdot u_y + v_z \cdot u_z $

$ = v \cdot u$

&nbsp; <br/>

## $u \cdot ( v + w ) = u \cdot v + u \cdot  w$

$u \cdot ( v + w )$

$= u \cdot (v_x + w_x, v_y + w_y, v_z + w_z)$

$= u_x \cdot (v_x + w_x) + u_y \cdot (v_y + w_y) + u_z \cdot (v_z + w_z) $

$= u_x \cdot v_x + v_x \cdot w_x + u_y \cdot v_y + u_y \cdot w_y + u_z \cdot v_z + u_z \cdot w_z $

$= u_x \cdot v_x + u_y \cdot v_y + u_z \cdot v_z + u_x \cdot w_x + u_y \cdot w_y + u_z \cdot w_z $

$= u \cdot v + u \cdot w$

&nbsp; <br/>

## $ k(u \cdot v) = k(u) \cdot v = u \cdot (kv)$

$ k(u \cdot v)$

$= k(u_x \cdot v_x + u_y \cdot v_y + u_z \cdot v_z)$ 

$= k(u_x \cdot v_x) + k(u_y \cdot v_y) + k(u_z \cdot v_z)$ 

$= (ku_x) \cdot v_x + (ku_y) \cdot v_y + (ku_z) \cdot v_z$

$=(ku_x, ku_y, ku_z) \cdot (v_x, v_y, v_z)$

$=k(u) \cdot v$

$= u_x \cdot (kv_x) + u_y \cdot (kv_y) + u_z \cdot (kv_z)$

$=u \cdot k(v)$

&nbsp; <br/>

## $v \cdot v = \Vert v \Vert ^2$

$v \cdot v$

$= (v_x \cdot v_x + v_y \cdot v_y + v_z \cdot v_z)$

$= (v_x^2 + v_y^2 + v_z^2) $

$= \sqrt{(v_x^2 + v_y^2 + v_z^2)^2} $

$= \Vert v \Vert ^2$

&nbsp; <br/>

## $0 \cdot v = 0$

$ 0 \cdot v$

$= 0 \cdot v_x + 0 \cdot v_y + 0 \cdot v_z$

$= 0$

&nbsp; <br/>


# 내적 공식 증명

코사인 법칙 $c^2 = a^2 + b^2 - 2ab \cos{\theta}$ 를 이용해서 다음을 증명해보아라.
(a, b, c 는 삼각형 세 변의 길이, $\theta$는 a와 b의 사이 각도 이다.)

$u_x v_x + u_y v_y + u_z v_z = \Vert u \Vert \Vert v \Vert \cos{\theta}$

**풀이**

$ \Vert u \Vert \cdot \Vert v \Vert \cdot cos{\theta}$

$ = \frac{\Vert u \Vert ^2 + \Vert v \Vert ^2 - \Vert u - v \Vert^2 }{2}$

$ = \frac{(\sqrt{(u_x^2 + u_y^2 + u_z^2)}^2 + \sqrt{(v_x^2 + v_y^2 + v_z^2)}^2 - \sqrt{(u_x-v_x)^2 + (u_y-v_y)^2+(u_z-v_z)^2}^2}{2}$

$ = \frac{u_x^2 + u_y^2 + u_z^2 + v_x^2 + v_y^2 + v_z^2 -  (u_x^2 + v_x^2 - 2u_xv_x + u_y^2 + v_y^2  - 2u_yv_y + u_z^2 + v_z^2  - 2u_zv_z )}{2}$

$ = \frac{u_x^2 + u_y^2 + u_z^2 + v_x^2 + v_y^2 + v_z^2 - u_x^2 - v_x^2 + 2u_xv_x - u_y^2 - v_y^2 + 2u_yv_y - u_z^2 - v_z^2 + 2u_zv_z )}{2}$

$ = \frac{2u_xv_x + 2u_yv_y + 2u_zv_z}{2}$

$ = \frac{2(u_xv_x + u_yv_y + u_zv_z)}{2}$

$ = u_xv_x + u_yv_y + u_zv_z$

&nbsp; <br/>

# 직교화

$ n = (-2, 1)$ 이라고 하자. n을 이용해서 $g = (0, -9.8)$를 이용해서 서로 수직인 두 벡터로 분해하라.

<img src="/images/exercise/11.png">

&nbsp; <br/>


# 외적 문제 1

$ u = (-2, 1, 4), v = (3, -4, 1)$ 일 때, $w = u \times v$ 를 구하고 $w \cdot u = 0, w \cdot v = 0$ 임을 보여라.

**풀이**

$w = ((1 \cdot 1) - (4 \cdot -4), (4 \cdot 3)-(-2 \cdot 1), (-2 \cdot -4) - (1 \cdot 3))$

$ = (17, 14, 5)$

$w \cdot u = 17 \cdot -2 + 14 \cdot 1 + 5 \cdot 4 = -34 + 14 + 20 = 0$

$w \cdot v = 17 \cdot 3 + 14 \cdot -4 + 5 \cdot 1 = 51 + -56 + 5 = 0 $

&nbsp; <br/>

# 외적 문제 2

점 $A = (0, 0, 0), B = (0, 1, 3), C = (5, 1, 0)$이 하나의 삼각형을 정의한다고 하자. 이 삼각형에 수직인 벡터를 구하라.

**풀이**

$\vec w = \overline{AB} \times \overline{AC} = (0, 1, 3) \times (5, 1, 0) = (-3, 15, -5) $

&nbsp; <br/>

# 외적 증명 문제

$\Vert u \times v \Vert = \Vert u \Vert \Vert v \Vert \sin{\theta} $ 임을 증명하여라
삼각함수의 항등식 : $(\cos^2{\theta} + \sin^2{\theta} = 1)$

**풀이**

$ \Vert u \Vert \Vert v \Vert \sin{\theta} $

$= \sqrt{u_x^2 + u_y^2 + u_z^2} \cdot \sqrt{v_x^2 + v_y^2 + v_z^2} \cdot \sin{\theta}$

$=\sqrt{(u_x^2+ u_y^2 + u_z^2) \cdot (v_x^2 + v_y^2 + v_z^2)} \cdot \sin{\theta}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2} \cdot \sin{\theta}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2} \cdot \sqrt{1 - \cos^2{\theta}}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2} \cdot \sqrt{1 - \cos^2{\theta}}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2} \cdot \sqrt{1 - (\frac{(u \cdot v)^2}{\Vert u \Vert ^2 \Vert v \Vert^2})}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2} \cdot \sqrt{(\frac{\Vert u \Vert ^2 \Vert v \Vert^2 -  (u \cdot v)^2}{\Vert u \Vert ^2 \Vert v \Vert^2})}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2} \cdot \sqrt{(\frac{\Vert u \Vert ^2 \Vert v \Vert^2 -  (u \cdot v)^2}{\Vert u \Vert ^2 \Vert v \Vert^2})}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2} \cdot \sqrt{(\frac{\Vert u \Vert ^2 \Vert v \Vert^2 -  (u \cdot v)^2}{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2})}$

$= \cdot \sqrt{\Vert u \Vert ^2 \Vert v \Vert^2 -  (u \cdot v)^2}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2 -  (u \cdot v)^2}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2 -  (u_xv_x + u_yv_y + u_zv_z)^2}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2 -  ( u_x^2v_x^2 + 2u_xv_xu_yv_y  +  u_y^2v_y^2 + 2u_yv_yu_zv_z + u_z^2v_z^2 + 2u_xv_xu_zv_z))}$

$=\sqrt{u_x^2v_x^2 + u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_y^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2+u_z^2v_z^2 - u_x^2v_x^2 - 2u_xv_xu_yv_y  -  u_y^2v_y^2 - 2u_yv_yu_zv_z - u_z^2v_z^2 - 2u_xv_xu_zv_z}$

$=\sqrt{ u_x^2v_y^2 + u_x^2v_z^2+u_y^2v_x^2+u_y^2v_z^2+u_z^2v_x^2+u_z^2v_y^2 - 2u_xv_xu_yv_y - 2u_yv_yu_zv_z - 2u_xv_xu_zv_z}$

$=\sqrt{ (u_y^2v_z^2 - 2u_yv_yu_zv_z + u_z^2v_y^2) + (u_z^2v_x^2 - 2u_xv_xu_zv_z + u_x^2v_z^2 ) + (u_x^2v_y^2 - 2u_xv_xu_yv_y + u_y^2v_x^2 ) }$

$=\sqrt{(u_yv_z - u_zv_y)^2+(u_zv_x - u_xv_z)^2 + (u_xv_y - u_yv_x)^2}$

$=\Vert u_yv_z - u_zv_y , u_zv_x - u_xv_z, u_xv_y - u_yv_x \Vert$

$=\Vert u \times x \Vert $

&nbsp; <br/>

# 평행사변형의 넓이

<img src="/images/exercise/15.png">

위 평행사변형의 넓이의 면적이 $\Vert u \times v \Vert$ 와 같음을 증명하여라

**풀이**

$ \Vert v \Vert h$

$ = \Vert v \Vert \Vert u \Vert \sin{\theta} $

$ = \Vert u \Vert \Vert v \Vert \sin{\theta} $

$=\Vert u \times v \Vert $

&nbsp; <br/>

# 외적의 결합법칙

$ u \times (v \times w) \neq (u \times v) \times w$ 를 만족하는 $u, v, w$의 예를 제시하라.

**풀이**

$u = (1, 1, 1), v = (1, 1, 0), w = (1, 0, 0)$

$ u \times (v \times w) = (1, 1, 1) \times (0, 0, -1) = (-1, 1, 0) $

$ (u \times v) \times w = (-1, 1, 0) \times (1, 0, 0) = (0, 0, -1)$

&nbsp; <br/>

# 외적 증명 문제 2

영벡터가 아닌 두 평행 벡터의 외적이 영벡터임을, $u \times ku = 0$를 증명하여라.

**풀이**

$\Vert u \times (ku_x, ku_y, ku_z) \Vert$

$ = \Vert (u_yku_z - u_zku_y, u_zku_x - u_xku_z, u_xku_y - u_yku_x) \Vert $

$ = \sqrt{(u_yku_z - u_zku_y)^2 + (u_zku_x - u_xku_z) ^2 + (u_xku_y - u_yku_x)^2}$

$ = \sqrt{k^2(u_yu_z - u_yu_z)^2 + k^2(u_zu_x - u_xu_z)^2+ k^2(u_xu_y-u_xu_y)^2}$

$ = \sqrt{k^2(0)^2 + k^2(0)^2+ k^2(0)^2}$

$ = 0$

&nbsp; <br/>
