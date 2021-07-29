---
layout: default
title: "벡터 대수"
nav_order: 1
parent: "[수학] 벡터 대수"
use_math: true
---

# 벡터 대수
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 벡터 알아보기

## 벡터의 정의

크기와 방향을 모두 가진 수량을 가르키는 말

&nbsp;<br/>

## 벡터의 종류

* 힘 (방향 + 세기)
* 변위 (최종적인 이동 방향 + 거리)
* 속도 (방향 + 빠르기)

&nbsp;<br/>

## 벡터 표현방법

<img src="/images/Vector/VectorMain.png">

Directed Line Segment(지향 선분) : 방향이 있는 선분

&nbsp;<br/>

## 벡터의 상등

<img src="/images/Vector/VectorEqual.png">

두 벡터의 **길이가 같고**, **방향도 같으면** 상등이다.

&nbsp;<br/>

# 백터와 좌표계

## 3차원 벡터를 표현하는 방법

<img src="/images/Vector/VectorAppearCoordinate.png">

&nbsp;<br/>

## 같은 벡터, 다른 기준계

<img src="/images/Vector/VectorDifferentCordinate.png">

&nbsp;<br/>

## 왼손잡이 좌표계, 오른손 잡이 좌표계

<img src="/images/Vector/LeftandRighthandedcns.png">

&nbsp;<br/>

# 벡터의 연산

## 벡터를 수식으로 표현하는 방법

* 2차원 벡터

x=3 이고 y=4 인 벡터 V
 
$\vec V$ = (3, 4) = $
\begin{bmatrix}
3  \cr
4
\end{bmatrix}
$

&nbsp;<br/>

* 3차원 벡터

x=2.3 이고 y=6.2 이고 Z=4.9 인 벡터 U

$\vec U$ = (2.3, 6.2, 4.9) = $
\begin{bmatrix}
2.3  \cr
6.2  \cr
4.9
\end{bmatrix}
$

&nbsp;<br/>

* 값 표현 방법

$\vec I = (3, 4, 5)$ 일 때<br/>

$\vec I_x = 3$

$\vec I_y = 4$

$\vec I_z = 5$


&nbsp;<br/>&nbsp;<br/>

## 벡터의 상등

두 벡터가 차수가 같고 모든 성분이 같으면 상등이다.

$\vec V, \vec U$ 에 대해서

$\dim (\vec V) = \dim (\vec U)$

$\vec V_x = \vec U_x$

$\vec V_y = \vec U_y$

...

$\vec V_n = \vec U_n$


이면 

$\vec V = \vec U$ 이므로 상등이다.

&nbsp;<br/>

## 벡터의 덧셈

두 벡터의 차수가 같으면 덧셈은 각각의 성분 별로 이루어진다.

$\vec V, \vec U$ 에 대해서

$\dim (\vec V) = \dim (\vec U)$ 일 때,

$\vec V + \vec U = (\vec V_x+\vec U_x, \vec V_y+\vec U_y, ... , \vec V_n+\vec U_n) $ 이다.

&nbsp;<br/>

## 스칼라 곱셈

벡터에는 스칼라를 곱할 수 있으며, 그 결과는 벡터다.

$\vec V, \{ k \in R  \}$ 일 때,

$k\vec V = (k\vec V_x, k\vec V_y, ... ,k \vec V_n)$ 이다.

&nbsp;<br/>

## 백터의 뺄셈

백터의 뺄셈은 백터 덧셈과 스칼라 곱셈을 통해서 구할 수 있다.

$\vec V, \vec U$ 에 대해서

$\dim (\vec V) = \dim (\vec U)$ 일 때,

$\vec V - \vec U$

$= \vec V + (-1 \cdot \vec U)$

$= (\vec V_x-\vec U_x, \vec V_y-\vec U_y, ... , \vec V_n-\vec U_n) $


&nbsp;<br/>

# 벡터의 크기와 단위벡터

## 벡터의 크기 표기법

$\vec V$ 의 크기는 $ \Vert V \Vert $ 로 표현한다.

$\vec V = (5,0)$ 일 때, $\Vert V \Vert = 5 $ 이다.

&nbsp;<br/>


## 피타고라스의 정리

* 2차원

$\vec V = (4,3)$ 일 때

<img src="/images/Vector/Pythagoras.png">

$\Vert \vec  V \Vert = \sqrt{ 4^2 + 3^2} = \sqrt{25} = 5$

$\therefore	\Vert \vec  V \Vert = 5$

&nbsp;<br/>

* 3차원

$\vec V = (x,y,z)$ 일 때

<img src="/images/Vector/3dPythagoras.png">

$\Vert \vec V\Vert $

$= \sqrt{ a^2 + y^2}$

$= \sqrt{ \sqrt{x^2 + z^2}^2 + y^2}$

$= \sqrt{ x^2 + z^2 + y^2}$

$\therefore	\Vert \vec V \Vert = \sqrt{ x^2 + z^2 + y^2}$

&nbsp;<br/>

## 단위 벡터

단위 벡터(방향 전용 벡터) : $\Vert \vec  V\Vert = 1$ 인 벡터

임의의 벡터의 크기를 단위 벡터로 만드는 것을 **정규화(normalization)** 한다고 부른다.

기호는 &nbsp; $\hat{}$  &nbsp;  를 사용한다. 
ex) $\hat{V}$

&nbsp;<br/>

## 단위 벡터 공식

벡터의 **각 성분을 벡터의 크기로 나누면** 정규화가 된다.

$\vec V = (x,y)$ 일 때,

$\hat{V} = (\frac{x}{\Vert V \Vert}, \frac{y}{\Vert V \Vert})$

&nbsp;<br/>

# 내적

**대응되는 각 성분들의 곱을 합산한 결과**

$\vec V, \vec U$ 가 있을 떄, 두 벡터의 내적은

$\vec V \cdot \vec U$ 로 정의된다.