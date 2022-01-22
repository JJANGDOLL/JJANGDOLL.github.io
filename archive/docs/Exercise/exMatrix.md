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

$(AB)_{i, \ast} = \begin{bmatrix} A_{i, \ast}B_{\ast , 1} & A_{i, \ast}B_{\ast , 2} & A_{i, \ast}B_{\ast , 3} \end{bmatrix}$

이고, <br/>

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

# 벡터 외적과 행렬 곱

벡터 외적을 다음과 같은 행렬 곱으로 표현할 수 있음을 증명하여라.

$\vec u \times \vec v = \begin{bmatrix} v_x & v_y & v_z \end{bmatrix}\begin{bmatrix} 0 & u_z & -u_y \cr -u_z & 0 & u_x \cr u_y & -u_x & 0 \end{bmatrix} $

&nbsp;<br/>

**풀이**

$\begin{bmatrix} v_x & v_y & v_z \end{bmatrix}\begin{bmatrix} 0 & u_z & -u_y \cr -u_z & 0 & u_x \cr u_y & -u_x & 0 \end{bmatrix}$

$= [v_y \cdot -u_z + v_z \cdot u_y , v_x \cdot u_z + v_z \cdot -u_x, v_x \cdot -u_y + v_y \cdot u_x] $

$= (u_yv_z -v_yu_z, u_zv_x - u_xv_z, u_xv_y - u_yv_x)$

$= \vec u \times \vec v$

&nbsp;<br/>

# 역행렬 문제 1

$A = \begin{bmatrix} 2 & 0 & 1 \cr 0 & -1 & -3 \cr 0 & 0 & 1 \end{bmatrix} $ 이라고 하고, $B = \begin{bmatrix} \frac{1}{2} & 0 & -\frac{1}{2} \cr 0 & -1 & -3 \cr 0 & 0 & 1 \end{bmatrix}$ 일 때, B는 A의 역행렬인가?

&nbsp;<br/>

**풀이**

$AA^{-1} = I$ 이므로, $AB = I$ 이여야 한다.

$\begin{bmatrix} 2 & 0 & 1 \cr 0 & -1 & -3 \cr 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} \frac{1}{2} & 0 & -\frac{1}{2} \cr 0 & -1 & -3 \cr 0 & 0 & 1 \end{bmatrix} = \begin{bmatrix} 1 & 0 & 0 \cr 0 & 1 & 0 \cr 0 & 0 & 1 \end{bmatrix}$

$\therefore B = A^{-1}$

&nbsp;<br/>

# 역행렬 문제 2

$A = \begin{bmatrix} 1 & 2 \cr 3 & 4 \end{bmatrix}$이고, $B = \begin{bmatrix} -2 & 1 \cr \frac{3}{2} & \frac{1}{2} \end{bmatrix}$ 일 때 $B$ 는 $A$의 역행렬인가?

&nbsp;<br/>

**풀이**

$AA^{-1} = I$ 이므로, $AB = I$ 이여야 한다.

$\begin{bmatrix} 1 & 2 \cr 3 & 4 \end{bmatrix}\begin{bmatrix} -2 & 1 \cr \frac{3}{2} & \frac{1}{2} \end{bmatrix} = \begin{bmatrix} 1 & 2 \cr 0 & 5 \end{bmatrix}$

&nbsp;<br/>

# 행렬식 문제

## 문제 1

$\begin{bmatrix} 21 & -4 \cr 10 & 7 \end{bmatrix}$

&nbsp;<br/>

**풀이**

$det\begin{bmatrix} 21 & -4 \cr 10 & 7 \end{bmatrix} = 21 \cdot 7 - (-4) \cdot 10 = 147 + 40 = 187$

&nbsp;<br/>

## 문제 2

$\begin{bmatrix} 2 & 0 & 0 \cr 0 & 3 & 0 \cr 0 & 0 & 7 \end{bmatrix}$

&nbsp;<br/>

**풀이**

$det \begin{bmatrix} 2 & 0 & 0 \cr 0 & 3 & 0 \cr 0 & 0 & 7 \end{bmatrix} = 2(3 \cdot 7 - 0 \cdot 0) = 42$

&nbsp;<br/>

# 역행렬 문제

## 문제 1

$M = \begin{bmatrix} 21 & -4 \cr 10 & 7 \end{bmatrix}$

&nbsp;<br/>

**풀이**

$det M = 187$ 이므로 역행렬이 존재.

&nbsp;<br/>


$M ^{-1} $

$ = \frac{1}{detM}M^\ast $

$ = \frac{1}{187} \begin{bmatrix} 7 & -4 \cr 10 & 21 \end{bmatrix}$

&nbsp;<br/>

## 문제 2

$M = \begin{bmatrix} 2 & 0 & 0 \cr 0 & 3 & 0 \cr 0 & 0 & 7 \end{bmatrix}$

&nbsp;<br/>

**풀이**

$det M = 42$ 이므로 역행렬이 존재.

$C_M^{T} = \begin{bmatrix} (-1)^{1+1}det\overline{M_{11}} & (-1)^{1+2}\overline{M_{12}} & (-1)^{1+3}\overline{M_{13}} \cr (-1)^{2+1}det\overline{M_{21}} & (-1)^{2+2}\overline{M_{22}} & (-1)^{2+3}\overline{M_{23}} \cr (-1)^{3+1}det\overline{M_{31}} & (-1)^{3+2}\overline{M_{32}} & (-1)^{3+3}\overline{M_{33}} \end{bmatrix}^{T} = \begin{bmatrix} 21 & 0 & 0 \cr 0 & 14 & 0 \cr 0 & 0 & 6 \end{bmatrix}^{T} = \begin{bmatrix} 21 & 0 & 0 \cr 0 & 14 & 0 \cr 0 & 0 & 6 \end{bmatrix}$

&nbsp;<br/>


$M ^{-1} $

$ = \frac{1}{detM}M^\ast $

$ = \frac{1}{42} \begin{bmatrix} 21 & 0 & 0 \cr 0 & 14 & 0 \cr 0 & 0 & 6 \end{bmatrix}$

&nbsp;<br/>

# 가역행렬 판별 문제

다음 행렬은 가역인가?

$M = \begin{bmatrix} 1 & 2 & 3 \cr 0 & 4 & 5 \cr 0 & 0 & 0 \end{bmatrix}$

&nbsp;<br/>

**풀이**

$detM = 0$

따라서 가역행렬이 아니다.

&nbsp;<br/>

#  가역행렬 증명 문제

$A$ 가 가역행렬 일 때, $(A^{-1})^T = (A^T)^{-1}$ 임을 보여라.

&nbsp;<br/>

**풀이**

양 변에 $A^T$를 곱함

$A^T(A^{-1})^T = A^T(A^T)^{-1}$ 

$(AA^{-1})^T = A^T(A^{-1})^T$

$I^T = (AA^{-1})^T $

$I^T = I^T$

&nbsp;<br/>

# 증명문제 1

$A$ 와 $B$ 가 $n \times n$ 행렬일 때, $det(AB) = det A \cdot det B$ 라는 등식이 성립한다. <br/>
$I = 1$ 이고, $A$ 가 가역행렬일 때 $det A^{-1} = \frac{1}{detA}$ 임을 증명하여라. <br/>

&nbsp;<br/>

**풀이**

$det(AA^{-1}) = det A \cdot det A^{-1}$

$1 = det A \cdot det A^{-1}$

$\therefore detA^{-1} = \frac{1}{detA} $

&nbsp;<br/>

# 증명문제 2

2차원 행렬 행렬식 $\begin{bmatrix} u_x & u_y \cr v_x & v_y \end{bmatrix}$ 가 $\vec u = (u_x, u_y), \vec v = (v_x , v_y)$ 로 정의되는 평행사변형의 넓이임을 증명하여라. <br/>

<img src="/images/exercise/Matrix/15.png">

&nbsp;<br/>

**풀이**

밑 변을 $ \Vert u \Vert$, 높이를 $h$ 라고 했을 떄

$\Vert u \Vert h$

$ = \Vert u \Vert \Vert v \Vert \sin{\theta}$

$ = \Vert u \times v \Vert$

$ = \Vert (0, 0, u_xv_y - u_yv_x) \Vert$

$ = \sqrt{(u_xv_y - u_yv_x)^2}$

$ = u_xv_y - u_yv_x$

$ = det \begin{bmatrix} u_x & u_y \cr v_x & v_y \end{bmatrix} $

&nbsp;<br/>

# 평행사변형의 넓이

두 벡터가 만드는 평행사변형의 넓이를 구하라.

&nbsp;<br/>

## 문제 1

$\vec u = (3, 0), \vec v = (1, 1)$

&nbsp;<br/>

**풀이**

$det \begin{bmatrix} 3 & 0 \cr 1 & 1 \end{bmatrix} = 3$

&nbsp;<br/>

## 문제 2

$\vec u = (-1, -1), \vec v = (0, 1)$

&nbsp;<br/>

**풀이**

$det \begin{bmatrix} -1 & -1 \cr 0 & 1 \end{bmatrix} = 1$

&nbsp;<br/>


