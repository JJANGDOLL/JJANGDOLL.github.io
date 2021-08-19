---
layout: default
title: "Transformation Exercise"
nav_order: 1
parent: "Exercise"
use_math: true
---

# 변환 연습문제
{: .no_toc}

DirectX 12를 이용한 3D 게임 프로그래밍 입문 문제풀이

3장 변환

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 선형변환과 표준 행렬 1

변환 $\tau : \mathbb{R}^3 \rightarrow^3 $ 이 $\tau (x, y, z) = (x + y, x - 3, z)$로 정의된다고 하자. 이 $\tau$ 는 선형변환인가? 그렇다면 그에 해당하는 표준 행렬을 구하여라.

**풀이**

$2\tau (x, y, z) = 2(x+y, x-3, z) = (2x + 2y, 2x - 6, 2z)$

$\tau 2(x, y, z) = \tau(2x, 2y, 2z) = (2x + 2y , 2x - 3, 2z)$

$2\tau (x, y, z) \neq \tau 2(x, y, z)$

선형변환이 아니다.

&nbsp;<br/>

# 선형변환과 표준 행렬 2

변환 $\tau : \mathbb{R}^3 \rightarrow^3 $ 이 $\tau (x, y, z) = (3x  + 4z, 2x - z, x + y + z)$로 정의된다고 하자. 이 $\tau$ 는 선형변환인가? 그렇다면 그에 해당하는 표준 행렬을 구하여라.

**풀이**

$2\tau (x, y, z) = 2(3x + 4z, 2x - z x + y + z) = (6x + 8z, 4x - 2z, 2x + 2y + 2z)$

$\tau 2(x, y, z) = \tau (2x, 2y, 2z) = (6x + 8z, 4x - 2z, 2x + 2y + 2z)$

$2\tau (x, y, z) = \tau 2(x, y, z)$

선형변환이다.

$\tau (u) = xt(i) + yt(j) + zt(k) = [x, y, z]\begin{bmatrix} \leftarrow & t(i) & \rightarrow \cr \leftarrow & t(j) & \rightarrow \cr \leftarrow & t(k) & \rightarrow \end{bmatrix} = [x, y, z]\begin{bmatrix} 3 & 2 & 1 \cr 0 & 0 & 1 \cr 4 & -1 & 1 \end{bmatrix}$

&nbsp;<br/>

# 선형변환과 표준 행렬 3

$\tau : \mathbb{R}^3 \rightarrow \mathbb{R}^3 $ 가 선형변환이라고 하자. 그리고 $\tau(1, 0, 0) = (3, 1, 2), \tau(0, 1, 0) =  (2, -1, 3), \tau(0, 0, 1) = (4, 0, 2)$ 이라고 하자. $\tau (1, 1, 1)$ 를 구하여라.

**풀이**

$\tau(1, 1, 1) = 1t(i) + 1t(j) + 1t(k) = [1, 1, 1]\begin{bmatrix} 3 & 1 & 2 \cr 2 & -1 & 3 \cr 4  & 0 & 2 \end{bmatrix} = \begin{bmatrix} 9 & 0 & 7 \end{bmatrix}$

&nbsp;<br/>

# 비례 행렬 기초

주어진 물체를 x 축을 따라 2단위, y 축을 따라  -3 단위 만큼 비례하되 z 성분으 변하지 않는 비례 행렬을 구하여라

**풀이**

비례 행렬 : $\begin{bmatrix} S_X & 0 & 0 \cr 0 & S_Y & 0 \cr 0 & 0 & S_Z \end{bmatrix}$ 

$\begin{bmatrix} 2 & 0 & 0 \cr 0 & -3 & 0 \cr 0 & 0 & 0 \end{bmatrix}$

&nbsp;<br/>

# 회전 행렬 기초

$(1, 1, 1)$ 을 축으로 $30^{\circ}$ 회전하는 회전 행렬을 구하라.

$\Vert (x, y, z) \Vert = 1$  

$\therefore (x, y, z) = (\frac{1}{\sqrt{3}}, \frac{1}{\sqrt{3}}, \frac{1}{\sqrt{3}})$

$\cos{30^{\circ}} = \frac{\sqrt{3}}{2}, \sin{30^{\circ}}= \frac{1}{2}$

회전 행렬

$
\begin{bmatrix}
     \frac{\sqrt{3}}{2} + \frac{1}{3}( 1 - \frac{\sqrt{3}}{2}) & \frac{1}{3}(1 - \frac{\sqrt{3}}{2}) + \frac{1}{\sqrt{3}} \frac{1}{2} & \frac{1}{3}(1 - \frac{\sqrt{3}}{2}) - \frac{1}{\sqrt{3}}\frac{1}{2} \cr
     \frac{1}{3}(1 - \frac{\sqrt{3}}{2}) - \frac{1}{\sqrt{3}}\frac{1}{2} & \frac{\sqrt{3}}{2} + \frac{1}{3}(1- \frac{\sqrt{3}}{2}) & (1 - \frac{\sqrt{3}}{2})\frac{1}{3} + \frac{1}{\sqrt{3}}\frac{1}{2} \cr
     \frac{1}{3}(1 - \frac{\sqrt{3}}{2}) + \frac{1}{\sqrt{3}}\frac{1}{2} & \frac{1}{3}(1-\frac{\sqrt{3}}{2}) - \frac{1}{\sqrt{3}}\frac{1}{2} & \frac{\sqrt{3}}{2} + \frac{1}{3}(1 - \frac{\sqrt{3}}{2})
\end{bmatrix}
$

$
{=}
\begin{bmatrix}
     \frac{\sqrt{3}}{2} + \frac{2 - \sqrt{3}}{6} & (\frac{1}{3} - \frac{\sqrt{3}}{6}) + \frac{\sqrt{3}}{6} & \frac{1 - \sqrt{3}}{3} \cr
     \frac{1 - \sqrt{3}}{3} & \frac{\sqrt{3}}{2} + \frac{2 - \sqrt{3}}{6} & (\frac{1}{3} - \frac{\sqrt{3}}{6}) + \frac{\sqrt{3}}{6} \cr
     (\frac{1}{3} - \frac{\sqrt{3}}{6}) + \frac{\sqrt{3}}{6} & \frac{1 - \sqrt{3}}{3} & \frac{\sqrt{3}}{2} + \frac{2 - \sqrt{3}}{6})
\end{bmatrix}
$

$
{=}
\begin{bmatrix}
    \frac{2\sqrt{3} + 2}{6} & \frac{1}{3} & \frac{1 - \sqrt{3}}{3} \cr \frac{1 - \sqrt{3}}{3} & \frac{2\sqrt{3} + 2}{6} & \frac{1}{3} \cr \frac{1}{3} & \frac{1 - \sqrt{3}}{3} & \frac{2\sqrt{3} + 2}{6}
\end{bmatrix}
$

&nbsp;<br/>

# 이동 행렬 기초

주어진 물체를 $x$ 축을 따라 4단위, $z$ 축을 따라 -9 단위 만큼 이동하되 $y$ 성분은 변경하지 않는 이동 명령을 구축하라.

**풀이**

이동 행렬 

$
\tau =
\begin{bmatrix}
    1 & 0 & 0 & 0 \cr
    0 & 1 & 0 & 0 \cr
    0 & 0 & 1 & 0 \cr
    b_x & b_y & b_z & 1
\end{bmatrix} =
\begin{bmatrix}
    1 & 0 & 0 & 0 \cr
    0 & 1 & 0 & 0 \cr
    0 & 0 & 1 & 0 \cr
    4 & 0 & -9 & 1
\end{bmatrix}
$

&nbsp;<br/>
