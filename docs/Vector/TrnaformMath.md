---
layout: default
title: "변환"
nav_order: 20
parent: "[Math] 게임 기초 수학"
use_math: true
---

# 변환
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 변환

3차원 그래픽은 3차원 세계의 물체들을 기하학적으로 묘사한다.<br/>
좀 더 구체적으로 말하면, 물체의 외부 표면을 근사(approximation)한 일련의 삼각형들로 물체를 표현한다.<br/>
하지만, 물체가 전혀 움직이지 않는다면 그다지 흥미롭지는 않을 것 이다.<br/>
따라서 물체를 나타내는 기하학적 구조를 어떤 형태로든 변환(Transformation)할 필요가 있다.<br/>
3차원 그래픽에 쓰이는 주된 기하학적 변화는 이동변환, 회전변환, 비례변환 이다.<br/>

&nbsp;<br/>

# 선형변환

## 선형변환 정의 정의

함수 $t$가 있다고 가정하고, $t(\vec v) = t(x, y, z) = ({x}', {y}' ,{z}')$ 를 만족할 때만 $t$를 가르켜서 **선형변환(linear transformation)** 이라고 부른다.

$t(\vec u+ \vec v) = t(\vec u) + t(\vec v)$

$t(k \vec u) = kt( \vec u)$

&nbsp;<br/>

## 선형변환 예시

$t(x, y, z) = (x^2, y^2, z^2)$ 라고 가정하자. 이 함수는 선형 변환이 아니다.<br/>

왜냐하면, $k=2, \vec u = (1, 2, 3)$ 이라고 할 때, <br/>

$t(ku) = t(2, 4, 6) = (4, 16, 36)$

$2t(u) = 2(1, 4, 9) = (2, 8, 18)$

$t(k \vec  u) \neq kt( \vec u)$ 이므로 선형 변환이 아니다.

만약 $t$가 선형 변환이라면 아래 성질이 성립한다.

$t(au + bv + cw) = t(au + (bv+cw))$

$ = at(u) + t(bv+cw)$

$ = at(u) + bt(v) + ct(w)$

&nbsp;<br/>

## 선형변환의 행렬 표현

$\vec u = (x, y, z)$ 라고 할 때, 항상 아래처럼 표현할 수 있다.<br/>

$u = (x, y, z) = xi + yj + zk = x(1, 0, 0) + y(0, 1, 0) + z(0, 0, 1)$

여기서의 $i, j, k$는 현재 좌표계의 축들과 같은 방향인 단위벡터 들인데, 이들을 $\mathbb{R}^3$ 에 대한 **표준기저벡터(Standard basis vector)** 라고 부른다.

여기서 $t$ 가 하나의 선형변환이라고 하면 다음이 성립한다.

$t(u) = t(xi + yj + zk) = xt(i) + yt(j) + zt(k)$

여기서 이제 벡터와 행렬의 곱으로 표현할 수 있다.

$t(u) = xt(i) + yt(j) + zt(k) = [x, y, z] \begin{bmatrix} \leftarrow & t(i) & \rightarrow \cr \leftarrow & t(j) & \rightarrow \cr \leftarrow & t(k) & \rightarrow \end{bmatrix} = [x, y, z] \begin{bmatrix} A_{11} & A_{12} & A_{13} \cr A_{21} & A_{22} & A_{23} \cr A_{31} & A_{32} & A_{33} \end{bmatrix}$

따라서, $t(i) = (A_{11}, A_{12}, A_{13}), t(j) = (A_{21}, A_{22} A_{33}), t(k) = (A_{31}, A_{32}, A_{33})$ 이다. 이러한 행렬 A를 선형변환 t의 행렬 표현(matrix representation) 이라고 부른다.

&nbsp;<br/>


# 비레(Scaling)

비례변환은 물체의 크기를 바꾸는 효과를 낸다. 

<img src="/images/transformation/transformationexample.png">

비레변환은 아래처럼 정의된다.

$S(x, y, z) = (s_xx, s_yy, s_zz)$

그렇다면 S가 선형변환을 만족하는지 확인해보자.

$S(u + v) = (s_x(u_x + v_x), s_y (u_y + v_y), s_z(u_z+v_z))$

$= (s_xu_x + s_xv_x, s_yu_y + s_yv_y, s_zu_z + s_zv_z)$

$= (s_xu_x, s_yu_y, s_zu_z) + (s_xv_x, s_yv_y, s_zv_z)$

$= S(u) + S(v)$

&nbsp;<br/>

$S(ku) = (s_xku_x, s_yku_y, s_zku_z)$

$= k(s_xu_x, s_yu_y, s_zu_z) $

$= kS(u) $

따라서 S는 선형변환이다. <br/>

여기에 S를 표준기저벡터를 각각 적용하고 S를 선형변환행렬로 표현하면<br/>

$S(i) = (s_x \cdot 1, s_y \cdot 0, s_z \cdot 0) = (s_x, 0, 0)$

$S(j) = (s_x \cdot 0, s_y \cdot 1, s_z \cdot 0) = (0, s_y, 0)$

$S(k) = (s_x \cdot 0, s_y \cdot 0, s_z \cdot 1) = (0, 0, s_z)$

$
S = 
\begin{bmatrix}
    s_x & 0 & 0 \cr
    0 & s_y & 0 \cr
    0 & 0 & s_z
\end{bmatrix}
$

이런 행렬을 **비례행렬(scaling matrix)** 이라고 부른다. <br/>

비례행렬의 역 행렬은 아래와 같다. <br/>

$
S^{-1} = 
\begin{bmatrix}
    \frac{1}{s_x} & 0 & 0 \cr
    0 & \frac{1}{s_y} & 0 \cr
    0 & 0 & \frac{1}{s_z}
\end{bmatrix}
$

&nbsp;<br/>

### 비례행렬의 예시

두 점$(-4, -4. 0), (4, 4, 0)$  으로 정의된 사각형을 $z$축은 그대로 두고 $x$ 축으로 0.5, $y$ 축으로 2 만큼 비례해보자. <br/>

비례행렬 S는 아래와 같다.

$S= \begin{bmatrix} 0.5 & 0 & 0 \cr 0 & 2 & 0 \cr 0 & 0 & 1 \end{bmatrix}$

이제 사각형을 변환하기 위해선, 최솟점과 최댓점에 이 행렬을 곱하면 된다.

$[-4, -4, 0]S = [-2, -8, 0], [4, 4, 0]S = [2, 8, 0]$

<img src="/images/transformation/scalingexample.png">

&nbsp;<br/>

# 회전

 벡터 $V$ 를 축 $n$에 대해서 회전하는 변환을 살펴보자.<br/>

 $n$ 은 $\Vert n \Vert = 1$ 인 단위 벡터이다. <br/>

&nbsp;<br/>
 

<img src="/images/transformation/rotation.png">

&nbsp;<br/>

위 그림의 벡터 $V$를 두 부분으로 분해한다. 하나는 $n$에 평행한 벡터 $proj_{n}(V)$ 이고, 다른 하나는 $V_\perp$ 이다.<br/>

이제 $n$을 기준으로 $V$ 의 회전을 알아볼 것 이므로 $proj_{n}(V)$ 는 회전에 대해서 불변(invariant) 이다. <br/>

그렇다면 수직인 부분의 벡터 $R_n(V_\perp)$ 만 구하면 된다. <br/>

우측 동그라미는 $R_n(V_\perp)$ 를 구하기 위한 2차원 좌표계이다. $V_\perp$ 는 두 기준 벡터중 하나로 사용되고, 나머지 하나는 $V_\perp$ 와 $n$에 수직인 벡터 $n \times V$ 로 구한다. <br/>

$R_n(V_{\perp}) = \cos{\theta}V_{\perp} + \sin{\theta}(n \times V)$

따라서, 아래와 같은 회전 공식을 이끌어 낼 수 있다.

$R_n(V) = proj_n(V) + R_n(V_{\perp})$

$= (n \cdot V)n + \cos{\theta}V_{\perp} + \sin{\theta}(n \times V)$

$= (n \cdot V)n + \cos{\theta}(V - (n \cdot V)n) + \sin{\theta}(n \times V)$

$ = \cos{\theta}V + (1- \cos{\theta})(n \cdot V)n + \sin{\theta}(n \times V)$

<br/>

## 2차원 회전 변환 행렬

<img src="/images/transformation/2drotation.png">

$\vec v = (x, y)$ 를 $\theta$ 만큼 회전시켜 $\vec u = ({x}', {y}')$ 를 만들었다. <br/>

$\overline{Ou} = \sqrt{x^2 + y^2}$

$\cos{a} = \frac{x}{\overline{Ou}} =  \frac{x}{ \sqrt{x^2 + y^2}}$

$\sin{a} = \frac{y}{\overline{Ou}} =  \frac{y}{ \sqrt{x^2 + y^2}}$

여기서 $u$ 는 $v$ 를 $+\theta$ 만큼 회전시킨 것 이므로,

${x}' = \sqrt{x^2 + y^2} \cos (a + \theta)$

${y}' = \sqrt{x^2 + y^2} \sin (a + \theta)$

이 나온다. 그러면 [삼각함수의 덧셈정리]() 로 인해서,