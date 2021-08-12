---
layout: default
title: "Matrix Exercise"
nav_order: 2
parent: "Exercise"
use_math: true
---

# 행렬 연습문제
{: .no_toc}

DirectX 12를 이용한 3D 게임 프로그래밍 입문 문제풀이

2장 행렬 대수

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# X 행렬 구하기

다음 행렬 방정식에서 X 를 구하여라

$3(\begin{bmatrix} -2 & 0 \cr 1 & 3 \end{bmatrix} - 2X) = 2 \begin{bmatrix} -2 & 0 \cr 1 & 3 \end{bmatrix}$

**풀이**

$3(M - 2X) = 2M$

$3M - 6X = 2M$

$M = 6X$

$ X = \frac{1}{6} M = \begin{bmatrix} -\frac{1}{3} & 0 \cr \frac{1}{6} & \frac{1}{2} \end{bmatrix} $

&nbsp;<br/>

# 행렬 곱셈

## 1번 문제

$
\begin{bmatrix}
    -2 & 0 & 3 \cr
    4 & 1 &  -1
\end{bmatrix}
\begin{bmatrix}
    2 & -1 \cr
    0 & 6 \cr
    2 & -3
\end{bmatrix} =
\begin{bmatrix}
    2 & -7 \cr
    6 & 5
\end{bmatrix}
$

&nbsp;<br/>

## 2번 문제

$
\begin{bmatrix}
    1 & 2 \cr
    3 & 4
\end{bmatrix}
\begin{bmatrix}
    -2 & 0 \cr
    1 & 1
\end{bmatrix} =
\begin{bmatrix}
    0 & 2 \cr
    -2 & 4
\end{bmatrix}
$

&nbsp;<br/>

## 3번 문제

$
\begin{bmatrix}
    2 & 0 & 2 \cr
    0 & -1 & -3 \cr
    0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
    1 \cr
    2 \cr
    1
\end{bmatrix} = 
\begin{bmatrix}
    4 \cr
    -5 \cr
    1
\end{bmatrix}
$

&nbsp;<br/>

# 전치 행렬

다음 행렬들의 전치를 구하라.

## 1번 문제

$
\begin{bmatrix}
    1 & 2 & 3
\end{bmatrix}^{T} = 
\begin{bmatrix}
    1 \cr
    2 \cr
    3
\end{bmatrix}
$

&nbsp;<br/>

## 2번 문제

$
\begin{bmatrix}
    x & y \cr
    z & w
\end{bmatrix}^{T} =
\begin{bmatrix}
    x & z \cr
    y & w
\end{bmatrix}
$

&nbsp;<br/>

## 3번 문제

$
\begin{bmatrix}
    1 & 2 \cr
    3 & 4 \cr
    5 & 6 \cr
    7 & 8
\end{bmatrix}^{T} =
\begin{bmatrix}
    1 & 3 & 5 & 7 \cr
    2 & 4 & 6 & 8
\end{bmatrix}
$

&nbsp;<br/>

#  벡터와 행렬의 곱

다음 일차결합들을 벡터와 행렬의 곱으로 표현하라

## 1번 문제

$2(1, 2, 3) -4(-5, 0, -1) + 3(2, -2, 3)$

$
{=}
[2, -4, 3]
\begin{bmatrix}
    1 & 2 & 3 \cr
    -5 & 0 & -1 \cr
    2 & -2 & 3
\end{bmatrix}
$

&nbsp;<br/>

## 2번 문제

$3(2, -4) + 2(1,4) -1(-2, -3) +5(1, 1)$

$
{=}
[3, 2, -1, 5]
\begin{bmatrix}
    2 & -4 \cr
    1 & 4 \cr
    -2 & -3 \cr
    1 & 1
\end{bmatrix}
$

&nbsp;<br/>

#