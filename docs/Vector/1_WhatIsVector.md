---
layout: default
title: "벡터 대수"
nav_order: 1
parent: "[수학] 벡터 대수"
use_math: true
---

# 벡터
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 벡터의 정의

크기와 방향을 모두 가진 수량을 가르키는 말

## 벡터의 종류

* 힘 (방향 + 세기)
* 변위 (최종적인 이동 방향 + 거리)
* 속도 (방향 + 빠르기)

## 벡터 표현방법

<img src="/images/Vector/VectorMain.png">

Directed Line Segment(지향 선분) : 방향이 있는 선분

## 벡터의 상등

<img src="/images/Vector/VectorEqual.png">

두 벡터의 **길이가 같고**, **방향도 같으면** 상등이다.

# 백터와 좌표계

## 3차원 벡터를 표현하는 방법

<img src="/images/Vector/VectorAppearCoordinate.png">

## 같은 벡터, 다른 기준계

<img src="/images/Vector/VectorDifferentCordinate.png">

## 왼손잡이 좌표계, 오른손 잡이 좌표계

<img src="/images/Vector/LeftandRighthandedcns.png">

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

* 3차원 벡터

x=2.3 이고 y=6.2 이고 Z=4.9 인 벡터 U

$\vec U$ = (2.3, 6.2, 4.9) = $
\begin{bmatrix}
2.3  \cr
6.2  \cr
4.9
\end{bmatrix}
$

* 값 표현 방법

$\vec I = (3, 4, 5)$ 일 때
$\vec I_x = 3$
$\vec I_y = 4$
$\vec I_z = 5$

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

## 벡터의 덧셈

두 벡터의 차수가 같으면 덧셈은 각각의 성분 별로 이루어진다.

$\vec V, \vec U$ 에 대해서

$\dim (\vec V) = \dim (\vec U)$ 일 때
$\vec V + \vec U = (\vec V_x+\vec U_x, \vec V_y+\vec U_y, ... , \vec V_n+\vec U_n) $ 이다.

## 벡터의 곱셈(스칼라 곱셈)

벡터에는 스칼라를 곱할 수 있으며, 그 결과는 벡터다.

$\vec V, \{ k \in R  \}$ 일 때
$k\vec V = (k\vec V_x, k\vec V_y, ... ,k \vec V_n)$ 이다.

## 백터의 뺄셈

백터의 뺄셈은 백터 덧셈과 스칼라 곱셈을 통해서 구할 수 있다.

$\vec V, \vec U$ 에 대해서

$\dim (\vec V) = \dim (\vec U)$ 일 때
$\vec V - \vec U$
$= \vec V + (-1 \cdot \vec U)$
$= (\vec V_x-\vec U_x, \vec V_y-\vec U_y, ... , \vec V_n-\vec U_n) $

