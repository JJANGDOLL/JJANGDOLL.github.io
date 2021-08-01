---
layout: default
title: "벡터 대수"
nav_order: 1
parent: "[Math] 게임 기초 수학"
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

# 벡터의 크기

## 벡터의 크기 표기법

$\vec V$ 의 크기는 $ \Vert V \Vert $ 로 표현한다.

$\vec V = (5,0)$ 일 때, $\Vert V \Vert = 5 $ 이다.

&nbsp;<br/>


## 벡터의 크기 구하기

**피타고라스의 정리를 이용한다.**

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

* N 차원

$\vec V = (x_1,x_2, \cdots , x_n)$ 일 때

$\therefore \Vert \vec V \Vert = \sqrt{ x_1^2 + x_2^2 + \cdots + x_n^2 }$

# 단위 벡터

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

**대응되는 각 성분들의 곱을 합산한 결과. 결과는 스칼라이다.**

$\vec V, \vec U$ 가 있을 떄, 두 벡터의 내적은

$\vec V \cdot \vec U$ 로 정의된다.



&nbsp;<br/>

## 내적 공식

$\vec V, \vec U$ 가 있을 때, 두 벡터의 내적에서

$\vec V \cdot \vec U = \Vert V \Vert \cdot \Vert U \Vert \cdot \cos{\theta}$ 이다.

&nbsp;<br/>

## 내적 공식 증명

아래와 같은 좌표에 벡터가 있다고 가정하자.

$\vec A = (x_1, y_1), \vec B = (x_2, y_2)$,

<img src="/images/Vector/VectorDotProductProof.png">

$\vec A \cdot \vec B = \Vert \vec A \Vert \cdot \Vert \vec B \Vert \cdot \cos{\theta} $

$ = \Vert \vec A \Vert \cdot \Vert \vec B \Vert \cdot \cos{(\alpha - \beta)} $

$ = \Vert \vec A \Vert \cdot \Vert \vec B \Vert \cdot (\cos{\alpha} \cdot \cos{\beta} + \sin{\alpha} \cdot \sin{\beta}) $

$ = \Vert \vec A \Vert \cdot \Vert \vec B \Vert \cdot \cos{\alpha} \cdot \cos{\beta} + \Vert \vec A \Vert \cdot  \Vert \vec B \Vert \cdot \sin{\alpha} \cdot \sin{\beta} $

$ = \Vert \vec A \Vert \cdot \cos{\alpha} \cdot \Vert \vec B \Vert \cdot \cos{\beta} + \Vert \vec A \Vert \cdot \sin{\alpha} \cdot \Vert \vec B \Vert \cdot \sin{\beta} $

$ = x_1 \cdot x_2 + y_1 \cdot y_2 $

$\therefore \vec A \cdot \vec B = \Vert A \Vert \cdot \Vert B \Vert \cdot \cos{\theta} = x_1 \cdot x_2 + y_1 \cdot y_2 $

&nbsp;<br/>

## 내적의 속성

<img src="/images/Vector/InnerProduct.png">

* $ \vec U \cdot \vec V = 0 $ 이면, $ \vec U \perp \vec V$ 이다.
* $ \vec U \cdot \vec V > 0 $ 이면, $ \theta < 90^\circ $ 이다.
* $ \vec U \cdot \vec V < 0 $ 이면, $ \theta > 90^\circ $ 이다.

&nbsp;<br/>

## 직교 투영(정사영)

<img src="/images/Vector/OrthogonalProjection.png">

$\vec V $와, 단위 벡터 $ \hat{n} $이 주어졌을 떄, $\vec p$ 를 내적을 이용해 $\vec V, \hat{n}$ 으로 나타내보자.<br/><br/>

$ \vec p = k \hat{n}$ 를 만족하는 스칼라 $ k $ 가 존재하는 것을 알 수 있다.

또한, $ \Vert \hat{n} \Vert = 1 $이므로, $\Vert \vec p \Vert = \Vert k \cdot \hat{n} \Vert = \vert k \vert \cdot \Vert \hat{n} \Vert = \vert k \vert$ 이고,

$ \vec p = k \hat{n} = (\Vert \vec V \Vert \cos{\theta}) \hat{n} $ 이다.

그런데 위 식에서 $ \hat{n}$ 은 단위백터 이므로 아래처럼 표현이 가능하다.

$\vec p = (\Vert \vec V \Vert \cos{\theta}) \cdot n = (\Vert \vec V \Vert * 1 \cos{\theta}) \cdot n = (\Vert \vec V \Vert \cdot \Vert \hat{n} \Vert  \cos{\theta}) \cdot n = ( \vec V \cdot \hat{n}) \hat{n}$

$\therefore \vec p = k \hat{n} =  ( \vec V \cdot \hat{n}) \hat{n} $

이처럼 $ \hat{n} $ 일 때,

$\vec p $를 $\hat{n}$ 에 대한 $\vec V$ 의 직교 투영(orthographic projection) 또는 정사영 이라고 부르며 아래처럼 표기한다.

$\vec p = proj_n(\vec V)$

여기서 $\vec w = prep_n(\vec V) = \vec V - \vec p $ 이므로,  $\vec V = \vec p + \vec w$ 라는 결론이 나온다.

만약 $ \vec n $ 이 단위벡터가 아니라면 $\vec n $ 을 정규화해서 단위 길이로 만들면 됩니다. 

그렇다면 위 투영 공식에 $ \vec n $ 을 단위벡터 $ \frac{\vec n}{\Vert n \Vert}$ 으로 대체하면 좀 더 일반적인 공식이 나온다.

$ \vec p = proj_n(\vec V) = (v \cdot \frac{\vec n}{\Vert n \Vert}) \cdot \frac{\vec n}{\Vert \vec n \Vert}  = \frac{(\vec V \cdot \vec n)}{\Vert \vec n \Vert ^ 2} \cdot n$

&nbsp;<br/>

## 정규직교

벡터 집합 $\{ \vec v_0, \vec v_1, \cdots, \vec v_{n-1} \} $의 모든 벡터가 단위 길이이고, 서로 직교라면 **정규직교** 집합이라고 부른다.

## 직교화

* 2차원

$\{v_0, v_1\}$를 직교화해서 정규직교 집합 $\{ w_0, w_1 \}$를 구해보자.

<img src="/images/Vector/2dOrthogonal.png">

우선 $w_0 = v_1 $로 시작해서, $v_1$ 이 $w_0$과 직교가 되게 만든다.

이것을 위해 $w_0$방향으로 작용하는 부분을 $v_1$ 에서 뺀다.

$w_1 = v_1 - proj_{w_0}(v_1)$ 

이제 서로 직교인 벡터들의 집합 $\{ w_0, w_1 \}$이 완성됬다.

$w_0, w_1$ 를 정규화 하면 정규직교 집합이 완성된다.

* 3차원

$\{v_0, v_1, v_2\}$를 직교화해서 정규직교 집합 $\{ w_0, w_1, w_2 \}$를 구해보자.

<img src="/images/Vector/3dOrthogonal.png">

2차원 때와 마찬가지로 우선 $w_0 = v_0$ 로 시작해서, $v_1$ 이 $w_0$ 와 직교가 되게 만든다.

$w_1 = v_1 - proj_{w_0}(v_1)$

이후 $w_2$ 는 $v_2$ 가 $w_0$ 과 $w_1$ 모두에 직교가 되도록 한다.

$w_2 = v_2 - proj_{w_0}(v_2) - proj_{w_1}(v_2)$

이러면 직교인 벡터들의 집합 $\{ w_0, w_1, w_2\}$ 가 만들어 졌다.

$w_0, w_1, w_2$ 를 정규화 하면 정규직교 집합이 완성된다.

&nbsp;<br/>

# 외적

**오직 3차원 벡터에 대해서만 정의된다. 결과는 벡터이다.**

두 3차원 벡터 $u, v$에 외적을 취하면, $u, v$모두에 직교인 또 다른 벡터 $w$ 가 나온다.

그렇다면, $w$ 는 $u$와도 직교이고 $v$ 와도 직교이다.

$u = (u_x, u_y, u_z), v = (v_x, v_y, v_z$$  라고 할 때 둘의 외적은 아래처럼 정의된다.

$w = u \times v = (u_yv_z - u_zv_y, u_zv_x - u_xv_z, u_xv_y - u_yv_x) $

&nbsp;<br/>

## 외적은 교환법칙이 성립되지 않는다

$u = (2, 1, 3), v = (2, 0, 0)$ 이라고 할 때, $w = u \times v, z = v \times u$ 를 계산하고 w가 u와 v에 교환법칙과 직교를 확인해보자.

* 교환 법칙

$ w = u \times v$

$ = (2, 1, 3) \times (2, 0, 0)$

$ = (1 \cdot 0 - 3 \cdot 0, 3 \cdot 2 - 2 \cdot 0, 2 \cdot 0 - 1 \cdot 2)$

$ = (0, 6, -2)$ <br/><br/>

$z = v \times u$

$ = (2, 0, 0) \times (2, 1, 3)$

$ = (0 \cdot 3 - 0 \cdot 1, 0 \cdot 2 - 2 \cdot 3, 2 \cdot 1 - 0 \cdot 2)$

$ = (0, -6, 2)$

$\therefore w \neq z, u \times v \neq v \times u $ <br/><br/>

<img src="/images/Vector/3dOuterProduct.png">

* 직교

$w$ 가 $v$에 직교인지 확인하는 방법은 $w \cdot v = 0$ 인지 확인해보면 된다.

$w \cdot v = (0, 6, -2) \cdot (2, 1, 3) = 0$

마찬가지로 $u$도 동일한지 확인한다.

$w \cdot u = (0, 6, -2) \cdot (2, 0, 0) = 0$

그러므로, $w$ 는 $u, v$에 대해서 직교이다.

&nbsp;<br/>

# 선형 대수

## 선형 독립

$ S = \{ \vec V_1, \vec V_2, \cdots, \vec V_n  \} $ 이고,

$ R = \{ x \vert x \in \mathbb{R}  \} $ 일 때,

$ R_1 \cdot S_1 + R_2 \cdot S_2 + ... R_n \cdot S_n = 0 $ 임을 만족하는

$ O = \{ x \vert x \in  R_1, R_2, \cdots, R_n \} $ 라고 할 때,

$ O = \{ 0 \}$ 이라면

$ S $ 는 선형 독립 이다.

&nbsp;<br/>

## 선형 종속

$ S = \{ \vec V_1, \vec V_2, \cdots, \vec V_n  \} $ 이고,

$ R = \{ x \vert x \in \mathbb{R}  \} $ 일 때,

$ R_1 \cdot S_1 + R_2 \cdot S_2 + ... R_n \cdot S_n = 0 $ 임을 만족하는

$ O = \{ x \vert x \in  R_1, R_2, \cdots, R_n \} $ 라고 할 떄,

$ O \neq \{ 0 \}$ 이라면

$ S $ 는 선형 종속 이다.

&nbsp;<br/>
