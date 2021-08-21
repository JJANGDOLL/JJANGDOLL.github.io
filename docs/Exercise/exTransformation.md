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

# 이동행렬과 비례행렬

먼저 $x$ 축을 따라 2단위, $y$ 축을 따라 -3 단위 비례하되 $z$ 축은 그대로 두는 비례변환을 수행하고,<br/>

$x$ 축으로 4단위, $y$ 축은 그대로, $z$ 축은 -9 단위 만큼 이동하는 이동 변환을 수행하는 하나의 변환 행렬을 제작하자.

**풀이**


$\begin{bmatrix} 2 & 0 & 0 & 0\cr 0 & -3 & 0 & 0\cr 0 & 0 & 1 & 0 \cr 0 & 0 & 0 & 1\end{bmatrix}\begin{bmatrix} 1 & 0 & 0 & 0 \cr 0 & 1 & 0 & 0 \cr 0 & 0 & 1 & 0 \cr 4 & 0 & -9 & 1 \end{bmatrix} = \begin{bmatrix} 2 & 0 & 0 & 0 \cr 0 & -3 & 0 & 0 \cr 0 & 0 & 1 & 0 \cr 4 & 0 & -9 & 1 \end{bmatrix}$ 

&nbsp;<br/>

# 회전행렬과 이동행렬

먼저 $y$ 축에 대해 $45^{\circ}$, $x$ 축으로 -2, $y$ 축으로 5, $z$ 축으로 1단위 이동하는 하나의 변환행렬을 구축하자.

**풀이**

$\begin{bmatrix} \frac{1}{\sqrt{2}} & 0 & -\frac{1}{\sqrt{2}} & 0 \cr 0 & 1 & 0 & 0 \cr \frac{1}{\sqrt{2}} & 0 & \frac{1}{\sqrt{2}} & 0 \cr 0 & 0 & 0 & 1 \end{bmatrix}\begin{bmatrix} 1 & 0 & 0 & 0 \cr 0 & 1 & 0 & 0 \cr 0 & 0 & 1 & 0 \cr -2 & 5 & 1 & 1\end{bmatrix} = \begin{bmatrix} \frac{1}{\sqrt{2}}& 0 & -\frac{1}{\sqrt{2}} & 0 \cr 0 & 1 & 0 & 0 \cr \frac{1}{\sqrt{2}} & 0 & \frac{1}{\sqrt{2}} & 0 \cr -2 & 5 & 1 & 1 \end{bmatrix}$

#  비례행렬의 적용

두 점 $(-4, -4, 0), (4, 4, 0)$ 으로 정의된 사각형을, $x$ 축을 따라 1.5, $y$ 축을 따라 0.75 만큼 비례하게 하자.

**풀이**

비례행렬
$
\begin{bmatrix}
    1.5 & 0 & 0 \cr
    0 & 0.75 & 0 \cr
    0 & 0 & 1 \cr
\end{bmatrix}
$

$
[-4, -4, 0]
\begin{bmatrix}
    1.5 & 0 & 0 \cr
    0 & 0.75 & 0 \cr
    0 & 0 & 1 \cr
\end{bmatrix} = [-6, 3, 0], 
[4, 4, 0]
\begin{bmatrix}
    1.5 & 0 & 0 \cr
    0 & 0.75 & 0 \cr
    0 & 0 & 1 \cr
\end{bmatrix} = [6, 3, 0]
$

&nbsp;<br/><br/>

# 회전행렬의 적용

두 점 $(-1, 0 -1), (1, 0, 1)$ 로 정의된 사각형을 $y$ 축을 따라 $-45^{\circ}$ 회전하라.

**풀이**

회전행렬
$
\begin{bmatrix}
    \frac{1}{\sqrt{2}} & 0 & \frac{1}{\sqrt{2}} \cr
    0 & 1 & 0 \cr
    -\frac{1}{\sqrt{2}} & 0 & \frac{1}{\sqrt{2}}
\end{bmatrix}
$


$
[-1, 0, -1]
\begin{bmatrix}
    \frac{1}{\sqrt{2}} & 0 & \frac{1}{\sqrt{2}} \cr
    0 & 1 & 0 \cr
    -\frac{1}{\sqrt{2}} & 0 & \frac{1}{\sqrt{2}}
\end{bmatrix} = [0, 0, -\frac{2}{\sqrt{2}}],
[1, 0, 1]
\begin{bmatrix}
    \frac{1}{\sqrt{2}} & 0 & \frac{1}{\sqrt{2}} \cr
    0 & 1 & 0 \cr
    -\frac{1}{\sqrt{2}} & 0 & \frac{1}{\sqrt{2}}
\end{bmatrix} = [0, 0, \frac{2}{\sqrt{2}}]
$

&nbsp;<br/>

# 이동행렬의 적용

두 점 $(-8, 2, 0), (-2, 8, 0)$ 으로 정의되는 사각형을 $x$ 축을 따라 -5, $y$ 축을 따라 -3, $z$ 축을 따라 4 만큼 이동하자.

이동행렬
$
\begin{bmatrix}
    1 & 0 & 0 & 0\cr
    0 & 1 & 0 & 0\cr
    0 & 0 & 1 & 0\cr
    -5 & -3 & 4 & 1
\end{bmatrix}
$

$
[-8, 2, 0, 1]
\begin{bmatrix}
    1 & 0 & 0 & 0\cr
    0 & 1 & 0 & 0\cr
    0 & 0 & 1 & 0\cr
    -5 & -3 & 4 & 1
\end{bmatrix} = [-13, -1, 4 ,1],
[-2, 8, 0, 1]
\begin{bmatrix}
    1 & 0 & 0 & 0\cr
    0 & 1 & 0 & 0\cr
    0 & 0 & 1 & 0\cr
    -5 & -3 & 4 & 1
\end{bmatrix} = [-7, 5, 4, 1]
$

&nbsp;<br/>

# 로드리게스의 회전 공식과 선형변환

$R_n(\vec v) = \cos{\theta}v + (1 - \cos{\theta})(n \cdot v)n + \sin{\theta}(n \times v)$ 가 선형변환임을 보이고, 표준행렬을 구하여라.

**풀이**

도저히 모르겠다 이건 ㅈㅈ

&nbsp;<br/>

# 회전 행렬와 정규직교

$R_y$ ($y$ 축 회전 행렬)의 행벡터들이 정규직교임을 증명하라.

**풀이**

$
R_y = 
\begin{bmatrix}
    \cos{\theta} & 0 & -\sin{\theta} \cr
    0 & 1 & 0 \cr
    \sin{\theta} & 0 & \cos{\theta}
\end{bmatrix}
$

$\Vert R_1 \Vert = \sqrt{\cos{\theta}^2 + 0^2 + \sin{\theta}^2} = 1$

$\Vert R_2 \Vert = \sqrt{0^2 + 1^2 + 0^2} = 1$

$\Vert R_3 \Vert = \sqrt{\sin{\theta}^2 + 0^2 + \cos{\theta}^2} = 1$

$R_1 \cdot R_3 = 0$

$R_2 \cdot R_3 = 0$

$R_1 \cdot R_3 = 0$

&nbsp;<br/><br/>
&nbsp;<br/><br/>
