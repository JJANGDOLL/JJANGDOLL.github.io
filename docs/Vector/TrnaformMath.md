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

## 선형변환 정의

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

# 벡터와 행렬

## 벡터의 투영 행렬 표현

벡터의 직교 투영을 더 풀게 되면 아래와 같은 공식이 나온다.

$proj_n(v) = (\vec v \cdot \hat{n}) \hat{n} = (v_x n_x + v_y n_y + v_z n_z)(n_x, n_y, n_z)$

여기서 $\vec v$를 표준 기저 벡터로 행렬 표현을 하게 되면

$proj_n(v_x) = (v_x ) = (1 \cdot n_x + 0 \cdot n_y + 0 \cdot n_z)(n_x, n_y, n_z) = (n_x^2, n_xn_y, n_xn_z)$

$proj_n(v_y) = (v_y) = (0 \cdot n_x + 1 \cdot n_y + 0 \cdot n_z)(n_x, n_y, n_z) = (n_xn_y, n_y^2, n_yn_z)$

$proj_n(v_z) = (v_z) = (0 \cdot n_x + 0 \cdot n_y + 1 \cdot n_z)(n_x, n_y, n_z) = (n_xn_z, n_yn_z, n_z^2)$

이렇게 표현할 수 있다.

결과적으로 투영 행렬은 아래처럼 표현할 수 있다.

$proj_n(v) = proj_n(v_x) + proj_n(v_y) + proj_n(v_z) = \begin{bmatrix} n_x^2 & n_xn_y & n_xn_z \cr n_xn_y & n_y^2 & n_yn_z \cr n_xn_z & n_yn_z & n_z^2 \end{bmatrix}$

&nbsp;<br/>

## 벡터의 외적 행렬 표현

$\vec u \times \vec v$

$= (u_yv_z -v_yu_z, u_zv_x - u_xv_z, u_xv_y - u_yv_x)$

$= [v_y \cdot -u_z + v_z \cdot u_y , v_x \cdot u_z + v_z \cdot -u_x, v_x \cdot -u_y + v_y \cdot u_x] $

$= \begin{bmatrix} v_x & v_y & v_z \end{bmatrix}\begin{bmatrix} 0 & u_z & -u_y \cr -u_z & 0 & u_x \cr u_y & -u_x & 0 \end{bmatrix}$

$\vec v$ 가 표준 기저 벡터라면 외적 행렬은 아래와 같다.

$M = \begin{bmatrix} 0 & u_z & -u_y \cr -u_z & 0 & u_x \cr u_y & -u_x & 0 \end{bmatrix}$

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
## 2차원 회전의 회전 행렬

<img src="/images/transformation/2drotation.png">

$\vec v = (x, y)$ 를 $\theta$ 만큼 회전시켜 $\vec u = ({x}', {y}')$ 를 만들었다. <br/>

$\overline{Ou} = \sqrt{x^2 + y^2}$

$\cos{a} = \frac{x}{\overline{Ou}} =  \frac{x}{ \sqrt{x^2 + y^2}}$

$\sin{a} = \frac{y}{\overline{Ou}} =  \frac{y}{ \sqrt{x^2 + y^2}}$

여기서 $u$ 는 $v$ 를 $+\theta$ 만큼 회전시킨 것 이므로,

${x}' = \sqrt{x^2 + y^2} \cos (a + \theta)$

${y}' = \sqrt{x^2 + y^2} \sin (a + \theta)$

이 나온다. 그러면 [삼각함수의 덧셈정리](/docs/Vector/1_trigonometric%20functions.html#삼각함수의-덧셈정리) 로 아래처럼 정리할 수 있다.

&nbsp;<br/>

${x}' = \sqrt{x^2 + y^2} \cos (a + \theta)$

$= \sqrt{x^2 + y^2} (\cos a \cos \theta - \sin a \sin \theta)$

$= \sqrt{x^2 + y^2} \cos a \cos \theta - \sqrt{x^2 + y^2} \sin a \sin \theta$

$ = \sqrt{x^2 + y^2} \frac{x}{ \sqrt{x^2 + y^2}} \cos \theta - \sqrt{x^2 + y^2} \frac{y}{ \sqrt{x^2 + y^2}} \sin \theta $

$ = x \cos \theta - y \sin \theta $

&nbsp;<br/>

${y}' = \sqrt{x^2 + y^2} \sin (a + \theta)$

$= \sqrt{x^2 + y^2} (\sin a \cos \theta + \cos a \sin \theta)$

$= \sqrt{x^2 + y^2} \frac{y}{ \sqrt{x^2 + y^2}} \cos \theta + \sqrt{x^2 + y^2} \frac{x}{ \sqrt{x^2 + y^2}} \sin \theta)$

$= y \cos \theta + x \sin \theta$

$= x \sin \theta + y \cos \theta$

&nbsp;<br/>

따라서 ${x}', {y}'$ 는 아래처럼 표현할 수 있다.

${x}' = x \cos \theta - y \sin \theta$

${y}' = x \sin \theta + y \cos \theta$

&nbsp;<br/>

$\therefore \begin{bmatrix} {x}' \cr {y}' \end{bmatrix} = \begin{bmatrix} \cos \theta & -\sin \theta \cr \sin \theta & \cos \theta \end{bmatrix} \begin{bmatrix} x \cr y \end{bmatrix}$a

&nbsp;<br/>

따라서, 회전 행렬 $R = \begin{bmatrix} \cos \theta & -\sin \theta \cr \sin \theta & \cos \theta \end{bmatrix}$ 이 된다.

&nbsp;<br/>

## 회전 행렬 $R$의 션형변환 증명

회전 행렬 $R = \begin{bmatrix} \cos \theta & -\sin \theta \cr \sin \theta & \cos \theta \end{bmatrix}$ 이 선형변환인지 확인해보자.

$R(k \vec u)$

$= \begin{bmatrix} \cos \theta & -\sin \theta \cr \sin \theta & \cos \theta \end{bmatrix}(k \begin{bmatrix} u_1 \cr u_2 \end{bmatrix})$

$= \begin{bmatrix} \cos \theta & -\sin \theta \cr \sin \theta & \cos \theta \end{bmatrix}\begin{bmatrix} ku_1 \cr ku_2 \end{bmatrix}$

$= \begin{bmatrix} k u_1 \cos \theta - k u_2 \sin \theta \cr k u_1 \sin \theta + k u_2 \cos \theta \end{bmatrix}$

&nbsp;<br/>

$(Rk) \vec u$

$= (\begin{bmatrix} \cos \theta & -\sin \theta \cr \sin \theta & \cos \theta \end{bmatrix} k) \begin{bmatrix} u_1 \cr u_2 \end{bmatrix}$

$= \begin{bmatrix} k  \cos \theta & -k \sin \theta \cr k \sin \theta & k \cos \theta \end{bmatrix} \begin{bmatrix} u_1 \cr u_2 \end{bmatrix} $

$= \begin{bmatrix} k u_1 \cos \theta - k u_2 \sin \theta \cr k u_1 \sin \theta + k u_2 \cos \theta \end{bmatrix}$

&nbsp;<br/>

$R(k \vec u) = (Rk) \vec u$

따라서, 회전 행렬 $R$은 선형변환이다.

&nbsp;<br/>

## 3차원 회전 행렬

2차원 회전에서 회전 행렬 $R = \begin{bmatrix} \cos \theta & -\sin \theta \cr \sin \theta & \cos \theta \end{bmatrix}$ 을 구했었다.<br/>

그렇다면 3차원은 2차원의 개념에서 확장시키면 간단하다.<br/>

$z$ 축을 추가하고, $R_z$ 는 $z$ 축을 기준으로 회전하는 것 이라고 하자. 하지만 축이 된 $z$ 는 회전해도 변하지 않으니 자기 자신이 나와야 한다.<br/>

따라서, $R_z$ 는 $R_z = \begin{bmatrix} \cos \theta & -\sin \theta & 0 \cr \sin \theta & \cos \theta & 0 \cr 0 & 0 & 1 \end{bmatrix}$

이러한 회전을 **오일러 회전** 이 라고 한다.

이것을 이용해서 나머지도 구할 수 있다.<br/>

$R_x = \begin{bmatrix} 1 & 0 & 0 \cr 0 & \cos \theta & -\sin \theta \cr 0 & \sin \theta & \cos \theta \end{bmatrix}, R_y = \begin{bmatrix} \cos \theta & 0 & \sin \theta \cr 0 & 1 & 0 \cr -\sin \theta & 0 & \cos \theta \end{bmatrix}, R_z = \begin{bmatrix} \cos \theta & -\sin \theta & 0 \cr \sin \theta & \cos \theta & 0 \cr 0 & 0 & 1 \end{bmatrix}$

위 변환 행렬 3가지를 모두 곱하면 각 축을 모두 고려한 회전을 나타내는 행렬이 나옵니다.<br/>

$ R = R_z(\alpha)R_y(\beta)R_x(\gamma) = \begin{bmatrix} \cos \alpha & -\sin \alpha & 0 \cr \sin \alpha & \cos \alpha & 0 \cr 0 & 0 & 1 \end{bmatrix}\begin{bmatrix} \cos \beta & 0 & \sin \beta \cr 0 & 1 & 0 \cr -\sin \beta & 0 & \cos \beta \end{bmatrix}\begin{bmatrix} 1 & 0 & 0 \cr 0 & \cos \gamma & -\sin \gamma \cr 0 & \sin \gamma & \cos \gamma \end{bmatrix}$

$= \begin{bmatrix} \cos \alpha \cos \beta & \cos \alpha \sin \beta \sin \gamma - \sin \alpha \cos \gamma & \cos \alpha \sin \beta \cos \gamma + \sin \alpha \sin \gamma \cr \sin \alpha \cos \beta & \sin \alpha \sin \beta \sin \gamma + \cos \alpha \cos \gamma & \sin \alpha \sin \beta \cos \gamma - \cos \alpha \sin \gamma \cr -\sin \beta & \cos \beta \sin \gamma & \cos \beta \cos \gamma \end{bmatrix}$

**여기엔 문제가 있다.**

위  $R$ 은 $z \rightarrow y \rightarrow x$ 순으로 연산을 했다. 하지만 연산 순서가 바뀌게 되면 $R$ 값이 또 바뀌게 된다. 또한 각 축이 겹쳐지는 문제가 발생할 수 있는데 이것을 **짐벌락** 이라고 부른다.

&nbsp;<br/>

## 로드리게스의 회전 공식

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

&nbsp;<br/>

$\vec v$ 가 표준기저 벡터라면,

$R_n(v) = proj_n(v) + R_n(v_{\perp})$

$= (n \cdot v)n + \cos{\theta}v_{\perp} + \sin{\theta}(n \times v)$

$= (n \cdot v)n + \cos{\theta}(V - (n \cdot v)n) + \sin{\theta}(n \times v)$

$ = \cos{\theta}v + (1- \cos{\theta})(n \cdot v)n + \sin{\theta}(n \times v)$

$ = \begin{bmatrix} \cos{\theta}  & 0 & 0 \cr 0 & \cos{\theta} & 0 \cr 0 & 0 & \cos{\theta} \end{bmatrix} + (1 - \cos{\theta})\begin{bmatrix} n_x^2 & n_xn_y & n_xn_z \cr n_xn_y & n_y^2 & n_yn_z \cr n_xn_z & n_yn_z & n_z^2 \end{bmatrix} + \sin{\theta}\begin{bmatrix} 0 & n_z & -n_y \cr -n_z & 0 & n_x \cr n_y & -n_x & 0 \end{bmatrix}$

$
{=}
\begin{bmatrix}
     \cos{\theta} + n_x^2( 1 - \cos{\theta}) & n_xn_y(1 - \cos{\theta}) + n_z \sin{\theta} & n_xn_z(1 - \cos{\theta}) - n_y\sin{\theta} \cr
     n_xn_y(1 - \cos{\theta}) - n_z\sin{\theta} & \cos{\theta} + n_y^2(1-\cos{\theta}) & (1 - \cos{\theta})n_yn_z + n_x\sin{\theta} \cr
     n_xn_z(1 - \cos{\theta}) + n_y\sin{\theta} & n_yn_z(1-\cos{\theta}) - n_x\sin{\theta} & \cos{\theta} + n_z^2(1 - \cos{\theta})
\end{bmatrix}
$

&nbsp;<br/>

이 회전행렬에는 흥미로운 성질이 있다.  각 행벡터는 단위 길이이고, 행벡터들은 서로 직교이다.<br/>

따라서, 행벡터들은 정규직교 이다. 직교행렬은 그 역행렬이 자신의 전치행렬과 같다는 속성이 있다.<br/>

$
R_n^{-1} = R_n^T =  
\begin{bmatrix}
     \cos{\theta} + n_x^2( 1 - \cos{\theta}) & n_xn_y(1 - \cos{\theta}) - n_z \sin{\theta} & n_xn_z(1 - \cos{\theta}) + n_y\sin{\theta} \cr
     n_xn_y(1 - \cos{\theta}) + n_z\sin{\theta} & \cos{\theta} + n_y^2(1-\cos{\theta}) & (1 - \cos{\theta})n_yn_z - n_x\sin{\theta} \cr
     n_xn_z(1 - \cos{\theta}) - n_y\sin{\theta} & n_yn_z(1-\cos{\theta}) + n_x\sin{\theta} & \cos{\theta} + n_z^2(1 - \cos{\theta})
\end{bmatrix}
$

**직교가 아닌 행렬보다는 역행렬을 쉽고 효율적으로 계산할 수 있는 직교행렬을 다루는 것이 더 바람직하다.**

특히 회전축이 $x$축, $y$축, $z$축 인 경우(즉, $n=(1, 0, 0), n = (0, 1, 0), n = (0, 0, 1)$ 이라면) 아주 간단해진다.

$R_x = \begin{bmatrix} 1 & 0 & 0 \cr 0 & \cos{\theta} & \sin{\theta} \cr 0 & -\sin{\theta} & \cos{\theta} \end{bmatrix}, R_y = \begin{bmatrix} \cos{\theta} & 0 & -\sin{\theta} \cr 0 & 1 & 0 \cr \sin{\theta} & 0 & \cos{\theta} \end{bmatrix}, R_z = \begin{bmatrix} \cos{\theta} & \sin{\theta} & 0 \cr -\sin{\theta} & \cos{\theta} & 0 \cr 0 & 0 & 1  \end{bmatrix}$

&nbsp;<br/>

## 회전 행렬 예시

두 점$(-1, 0, -1), (1, 0, 1)$ 로 사각형을 만들어서 $y$ 축에 대해 시계방향으로 $-30^{\circ}$ 로 회전한다고 하자. <br/>

이 경우에는 $n = (0, 1, 0)$ 이므로 $R_n$이 매우 간단해진다.

$y$ 축에 대한 회전 행렬은 아래와 같다.

$R_y = \begin{bmatrix} \cos{\theta} & 0 & -\sin{\theta} \cr 0 & 1 & 0 \cr \sin{\theta} & 0 & \cos{\theta} \end{bmatrix} = \begin{bmatrix} \cos{(-30^{\circ})} & 0 & -\sin{(-30^{\circ})} \cr 0 & 1 & 0 \cr \sin{(-30^{\circ})} & 0 & \cos{(-30^{\circ})} \end{bmatrix} = \begin{bmatrix} \frac{\sqrt{3}}{2} & 0 & \frac{1}{2} \cr 0 & 1 & 0 \cr -\frac{1}{2} & 0 & \frac{\sqrt{3}}{2} \end{bmatrix}$

이제 두 점에 $R_y$ 행렬만 곱하면 된다.

$[-1, 0, -1]\begin{bmatrix} \frac{\sqrt{3}}{2} & 0 & \frac{1}{2} \cr 0 & 1 & 0 \cr -\frac{1}{2} & 0 & \frac{\sqrt{3}}{2} \end{bmatrix} \approx [-0.36, 0, -1.36], [1, 0, 1]\begin{bmatrix} \frac{\sqrt{3}}{2} & 0 & \frac{1}{2} \cr 0 & 1 & 0 \cr -\frac{1}{2} & 0 & \frac{\sqrt{3}}{2} \end{bmatrix} \approx [0.36, 0, 1.36]$

&nbsp;<br/>

# 아핀변환

## 동차좌표

벡터는 위치와 무관한, 오직 방향과 크기만 서술하는 것 이므로 벡터에 대해서는 이동이 의미가 없다. <br/> 

벡터는 이동에 대해서 불편이여야 하고, 이동은 오직 점에만 적용되어야 한다.<br/>

동차좌표(homogeneous coordinate) 라는 것을 사용하여 점과 벡터를 동일한 방식으로 다룰 수 있다. <br/>

동차좌표는 3차원 벡터에 $w$ 성분을 추가한 (4-tuple) 형태인데, 주어진 동차좌표가 점을 나타내느냐 벡터를 나타내느냐는 이 $w$ 값이 결정한다.<br/>

* 벡터 $( x, y, z, 0)$
* 점 $(x, y, z, 1)$

로 표기한다.<br/>

점을 나타내는 동차좌표에서 $w=1$ 로 설정해야 점의 이동변환이 제대로 수행되며 <br/>

벡터를 나타내는 동차좌표에서 $w=0$ 로 설정해야 벡터의 이동변환이 제대로 수행된다. <br/>

&nbsp;<br/>

## 아핀변환의 정의

3차원 그래픽에 필요한 변환 중에는 선형변환으로 서술하지 못하는 것도 있다.<br/>

그래서 아핀변환이(affine transformation)라고 부르는 좀 더 넓은 부류의 함수들을 도입해야 한다. <br/>

아핀변환은 선형변환에 이동벡터 $b$ 를 더한 것이다. 수식으로 표현하면 아래와 같다.<br/>

$\alpha(u) = \tau(u) + b$

이것을 행렬로 다시 나타내보면 

$\alpha(u) = uA + b = [x, y, z]\begin{bmatrix} A_{11} & A_{12} & A_{13} \cr A_{21} & A_{22} & A_{23} \cr A_{31} & A_{32} & A_{33} \end{bmatrix} + [b_x, b_y, b_z] = [{x}', {y}', {z}']$

이다. 근데 $w=1$ 인 동차좌표를 도입하면 아편변환을 좀 더 간결하게 표현할 수 있다.<br/>

$\begin{bmatrix} A_{11} & A_{12} & A_{13} & 0 \cr A_{21} & A_{22} & A_{23} & 0 \cr A_{31} & A_{32} & A_{33} & 0 \cr b_x & b_y & b_z & 1 \end{bmatrix} + [b_x, b_y, b_z] = [{x}', {y}', {z}', 1]$

위 $4 \times 4$ 행렬을 아핀변환의 행렬 표현이라고 한다. 추가적으로 $b$ 는 이동(위치 변경) 을 나타낸 다는 것을 주목해야 한다.<br/>

벡터에서는 위치가 없으므로, 벡터에 대해서는 그러한 이동을 적용하면 안된다. 하지만 아핀변환의 선형변환 부분은 여전히 벡터에 적용해야 된다. <br/>

벡터의 동차좌표의 넷째 성분을 0$(w = 0)$로 설정하면 b에 의한 이동이 적용되지 않는다.<br/>

&nbsp;<br/>

## 이동 

주어진 인수를 그대로 돌려주는 선형변환을 **항등변환(identity transformation)** 이라고 부른다.<br/> 

$I(u) = u$ 이며, 이 선형변환의 행렬 표현은 단위 행렬이다.

<img src="/images/transformation/identity.png">

위 이동변환은 아래처럼 표현할 수 있다.

$\tau(u) = uI + b = u + b$

이 변환은 그냥  점 $u$를 $b$ 만큼 이동한다. $\tau$ 를 아래 행렬로 표현할 수 있다.

$
\tau =
\begin{bmatrix}
    1 & 0 & 0 & 0 \cr
    0 & 1 & 0 & 0 \cr
    0 & 0 & 1 & 0 \cr
    b_x & b_y & b_z & 1
\end{bmatrix}
$

이러한 행렬을 **이동행렬(translation matrix)** 라고 부른다

이동행렬의 역은 아래와 같다.

$
\tau ^{-1} =
\begin{bmatrix}
    1 & 0 & 0 & 0 \cr
    0 & 1 & 0 & 0 \cr
    0 & 0 & 1 & 0 \cr
    -b_x & -b_y & -b_z & 1
\end{bmatrix}
$

&nbsp;<br/>

## 이동 행렬의 예시

두 점$(-8, 2, 0), (-2, 8, 0)$ 으로 정의되는 사각형을 x축을 따라 12, y 축을 따라 -10 단위만큼 이동한다고 하자.(z 는 그대로둔다.) 이 이동에 해당하는 이동행렬은 아래와 같다.

$T = \begin{bmatrix} 1 & 0 & 0 & 0 \cr 0 & 1 & 0 & 0 \cr 0 & 0 & 1 & 0 \cr 12 & -10 & 0 & 1 \end{bmatrix}$

$[-8, 2, 0, 1]\begin{bmatrix} 1 & 0 & 0 & 0 \cr 0 & 1 & 0 & 0 \cr 0 & 0 & 1 & 0 \cr 12 & -10 & 0 & 1 \end{bmatrix} = [4, -8, 0,  1]$

$[-2, 8, 0, 1]\begin{bmatrix} 1 & 0 & 0 & 0 \cr 0 & 1 & 0 & 0 \cr 0 & 0 & 1 & 0 \cr 12 & -10 & 0 & 1 \end{bmatrix} = [10, -2, 0, 1]$

&nbsp;<br/>

## 비레와 회전의 아핀변환 행렬

$b = 0$ 이면 아핀변환은 그냥 보통의 선형변환과 같다. 그 어떤 선형변환이라도 $b=0$ 인 아핀변환으로 표현할 수 있다. <br/>

즉, 임의의 선형변환을 $4 \times 4$ 아핀행렬로 표기할 수 있다. 예를 들어 비례변환과 회전변환을 $4 \times 4$ 로 표현할 수 있다.<br/>

$S = \begin{bmatrix} S_x & 0 & 0 & 0 \cr 0 & S_y & 0 & 0 \cr 0 & 0 & S_z & 0 \cr 0 & 0 & 0 & 1 \end{bmatrix}$

$
R_n = 
\begin{bmatrix}
     \cos{\theta} + n_x^2( 1 - \cos{\theta}) & n_xn_y(1 - \cos{\theta}) + n_z \sin{\theta} & n_xn_z(1 - \cos{\theta}) - n_y\sin{\theta} & 0 \cr
     n_xn_y(1 - \cos{\theta}) - n_z\sin{\theta} & \cos{\theta} + n_y^2(1-\cos{\theta}) & (1 - \cos{\theta})n_yn_z + n_x\sin{\theta} & 0\cr
     n_xn_z(1 - \cos{\theta}) + n_y\sin{\theta} & n_yn_z(1-\cos{\theta}) - n_x\sin{\theta} & \cos{\theta} + n_z^2(1 - \cos{\theta} & 0 \cr
     0 & 0 & 0 & 1
\end{bmatrix}
$

&nbsp;<br/>

# 변환들의 합성

$S$ 가 비례행렬, $R$ 이 회전 행렬, $T$ 가 이동 행렬 이라고 하자. $i=0, 1, \cdots,  7$ 여덟 정점 $v_i$들로 이루어진 직육면체의 각 정점에 이 세 변환을 적용하여야 한다.<br/>

이를 수행하는 방법으로써 가장 먼저 떠오르는 방법은 행렬을 하나씩 차례로 곱하는 방법이다.<br/>

$((v_iS)R)T = ({v_i}'R)T = {v_i}''T = {v_i}'''$

그런데, 행렬 곱셈은 결합법칙을 만족한다. 따라서 아래처럼 표현을 해도 무관하다.<br/>

$v_i(SRT) = {v_i}'''$

이렇게 한다면 미리 계산한 $SRT$ 는 여러 변환을 하나로 합치는 효과를 낸다.<br/>

이러한 합성은 성능에 영향을 미친다. <br/>

20,000 개의 점으로 이루어진 3D 물체에 세 가지 기하 변환을 적용한다면 이해가 빠른데, 전자의 경우라면 벡터 대 행렬의 곱셈은 $20000 \times 3 = 60000$ 회의 계산이 필요하지만 후자의 경우 벡터 대 행렬의 곱셈 $20000 \times 2$ 회에 행렬 대 행렬 곱셈 2회 (총 40002회)면 충분하다. <br/>

주의해야 할 부분은 행렬 간 곱셈에서는 교환 법칙이 성립하지 않는 다는 것이다.<br/>

변환 후 회전하는 경우와$(TR)$와 회전 후 변환 $(RT)$ 의 결과가 다르므로 주의하자.

&nbsp;<br/>

