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

&nbsp;<br/>

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

# 벡터와 행렬의 곱 증명

다음 등식을 증명하라.

$
AB = 
\begin{bmatrix}
    A_{11} & A_{12} & A_{13} \cr
    A_{21} & A_{22} & A_{23} \cr
    A_{31} & A_{32} & A_{33}
\end{bmatrix}
\begin{bmatrix}
    B_{11} & B_{12} & B_{13} \cr
    B_{21} & B_{22} & B_{23} \cr
    B_{31} & B_{32} & B_{33}
\end{bmatrix} = 
\begin{bmatrix}
    \leftarrow & A_{1, \ast}B & \rightarrow \cr
    \leftarrow & A_{2, \ast}B & \rightarrow \cr
    \leftarrow & A_{3, \ast}B & \rightarrow \cr
\end{bmatrix}
$

&nbsp;<br/>

**풀이**

임의의 $i$ 번째 행에 대해서, $(AB)_{i,\ast}$ 이라고 했을 때,<br/>

$(AB)_{i, \ast} = \begin{bmatrix} A_{i, \ast}B_{\ast , 1} & A_{i, \ast}B_{\ast , 2} & A_{i, \ast}B_{\ast , 3} \end{bmatrix}$ 이고, <br/>

이것을 벡터와 행렬의 곱 나타내면 <br/>

$(AB)_{i, \ast} = \begin{bmatrix} A_{i, \ast}B_{\ast , 1} & A_{i, \ast}B_{\ast , 2} & A_{i, \ast}B_{\ast , 3} \end{bmatrix} = A_{i, \ast} \begin{bmatrix} B_{\ast, 1} & B_{\ast, 2} & B_{\ast, 3} \end{bmatrix} = A_{i, \ast}B$

행렬 $i$ 에 1를 대입한다면 $(AB)_{1, \ast} = A_{1, \ast}B$ 이 된다.

&nbsp;<br/>

# 벡터의 행렬의 곱 증명 2

다음 등식을 증명하여라.

$Au = \begin{bmatrix} A_{11} & A_{12} & A_{13} \cr A_{21} & A_{22} & A_{23} \cr A_{31} & A_{32} & A_{33} \end{bmatrix}\begin{bmatrix} x \cr y \cr z \end{bmatrix} = xA_{\ast, 1} + yA_{\ast, 2} + zA_{\ast, 3}$

&nbsp;<br/>

**풀이**

$\begin{bmatrix} A_{11} & A_{12} & A_{13} \cr A_{21} & A_{22} & A_{23} \cr A_{31} & A_{32} & A_{33} \end{bmatrix}\begin{bmatrix} x \cr y \cr z \end{bmatrix} = \begin{bmatrix} xA_{11} + yA_{12} + zA_{13} \cr xA_{21} + yA_{22} + zA_{23} \cr xA_{31} + yA_{32} + zA_{33} \end{bmatrix}$

$ = xA_{11} + yA_{12} + zA_{13} + xA_{21} + yA_{22} + zA_{23} +  xA_{31} + yA_{32} + zA_{33} $

$ = x(A_{11}, A_{21}, A_{31}) + y(A_{12}, A_{22}, A_{23}) + z(A_{13} , A_{23}, A_{33})$

$ = xA_{\ast, 1} + yA_{\ast, 2} + zA_{\ast, 3}$

&nbsp;<br/>
