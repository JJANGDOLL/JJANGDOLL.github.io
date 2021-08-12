---
layout: default
title: "행렬 대수"
nav_order: 10
parent: "[Math] 게임 기초 수학"
use_math: true
---

# 행렬 대수
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 행렬 알아보기

## 컴퓨터 그래픽에서 행렬이란?

3차원 컴퓨터 그래픽에서 행렬은 스케일이나 회전, 이동 같은 기하학적 변환을 간결하게 서술하는 데 사용하며,<br/>
점이나 벡터의 좌표를 한 기준계에서 다른 기준계로의 변환에서도 사용한다.

&nbsp;<br/>

## 행렬 기초

$m \times n$ 행렬 $M$ 은 $m$ 개의 행과 $n$개의 열로 이루어진 정사각 배열이다.<br/>
행렬을 구성하는 수 들을 **성분(entry)** 또는 **원소(element)** 라고 부른다.<br/>
행렬의 한 성분을 나타날 때 에는 아래 첨자 $M_{ij} $ 로 표현한다.<br/>


$A = \begin{bmatrix} 3.5 & 0 & 0 & 0 \cr 0  & 1 & 0 & 0 \cr 0&  0& 0.5 &0 \cr 2& -5 & \sqrt{2} & 1\end{bmatrix}, B = \begin{bmatrix} B_{11} & B_{12} \cr B_{21} & B_{22} \cr B_{31} & B_{32} \cr B_{41} & B_{42} \cr \end{bmatrix}, u = \begin{bmatrix}u_{1} & u_{2} &  u_{3} \end{bmatrix},  v = \begin{bmatrix} 1 \cr 2 \cr \sqrt{3} \cr \pi \end{bmatrix}$

* 행렬 A는 $4 \times 4$ 행렬이고, B는 $3 \times 2$, u는 $1 \times 3$, v는 $4 \times 1$ 이다.
* A의 4행 2열의 성분 (-5) 는 $A_{42}$ 로 표현한다.
* $u, v$는 행이나 열이 하나라는 점에서 특별한 행렬이다. 이것을 행벡터, 열벡터 라고 부른다.
  * 이들은 벡터를 행렬 형태로 표기할 때 쓰인다.

종종 한 행렬의 행을 한 번에 표기하는 법이 편리할 때가 있다. 

$ \begin{bmatrix} A_{11} & A_{12} & A_{13} \cr A_{21} & A_{22} & A_{23} \cr A_{31} & A_{32} & A_{33} \end{bmatrix} = \begin{bmatrix}\leftarrow  A_{1,\ast} \rightarrow \cr \leftarrow  A_{2,\ast} \rightarrow \cr \leftarrow  A_{3,\ast} \rightarrow \end{bmatrix}$

여기서 $A_{1,*} = [A_{11}, A_{12}, A_{13}]$ 를 의미한다.<br/>

마찬가지로, 열 벡터들도 아래처럼 표현할 수 있다.

$
\begin{bmatrix}
   A_{11} & A_{12} & A_{13} \cr
   A_{21} & A_{22} & A_{23} \cr
   A_{31} & A_{32} & A_{33}
\end{bmatrix} =
\begin{bmatrix}
    \uparrow & \uparrow&\uparrow \cr
    A_{\ast,1} &A_{\ast,2}&A_{\ast,3} \cr
    \downarrow & \downarrow&\downarrow
\end{bmatrix}
$

여기서,

$
A_{\ast,1} =
\begin{bmatrix}
   A_{11} \cr
   A_{21} \cr
   A_{31} 
\end{bmatrix}
$

를 의미한다.

&nbsp;<br/>

# 행렬 연산

다음 같은 행렬이 있다고 하자.

$
A = \begin{bmatrix}
    1   &   5   \cr
    -2  &   3   
\end{bmatrix},
B = \begin{bmatrix}
    6   &   2   \cr
    5   &   -8  
\end{bmatrix},
C = \begin{bmatrix}
    1   &   5   \cr
    -1   &   3  
\end{bmatrix},
D = \begin{bmatrix}
    2   &   1   &   -3  \cr
    -6  &   3   &   0   
\end{bmatrix}
$

&nbsp;<br/>

## 덧셈

$
A + B = 
\begin{bmatrix}
    1   &   5   \cr
    -2  &   3   
\end{bmatrix} + 
\begin{bmatrix}
    6   &   2   \cr
    5   &   -8  
\end{bmatrix} = 
\begin{bmatrix}
    1+6 & 5+2 \cr
    -2+5 & 3+(-8)
\end{bmatrix} =
\begin{bmatrix}
    7 & 7 \cr
    3 & -5
\end{bmatrix}
$

&nbsp;<br/>

## 등호

$A = C$

&nbsp;<br/>



## 뺄셈

$
A - B =
\begin{bmatrix}
    1   &   5   \cr
    -2  &   3   
\end{bmatrix} - 
\begin{bmatrix}
    6   &   2   \cr
    5   &   -8  
\end{bmatrix} =
\begin{bmatrix}
    1-6 & 5-2 \cr
    -2-5 & 3-(-8)
\end{bmatrix} =
\begin{bmatrix}
    -5 & 3 \cr
    -7 & 5
\end{bmatrix}
$

&nbsp;<br/>

## 행렬 곱셈

### 스칼라 곱

$
3D = 
3\begin{bmatrix}
    2   &   1   &   -3  \cr
    -6  &   3   &   0   
\end{bmatrix} = 
\begin{bmatrix}
    (3)2   &   (3)1   &   (3)-3  \cr
    (3)-6  &   (3)3   &   (3)0   
\end{bmatrix} =
\begin{bmatrix}
   6   &   3  &   -9  \cr
    -18  &   9   &   0  
\end{bmatrix}
$

&nbsp;<br/>

### 행렬 곱

$A = m \times n, B = n \times p$ 이라면 둘의 곱 $AB$ 를 정의할 수 있다.

$AB$ 는 $m \times p$ 행렬이 된다. 이것을 $C$ 라고 하자.

$C$ 의 $ij$ 번째 성분은 $A_{i,\ast}$ 와 $B_{\ast,j}$의 내적이다.

$C_{ij} = A_{i,\ast} \cdot B_{\ast,j}$

$
A = 
\begin{bmatrix}
    -1  &   5   &   -4  \cr
    3   &   2   &   1   
\end{bmatrix},
B = 
\begin{bmatrix}
    2   &   1   &   0   \cr
    0   &   -2  &   1   \cr
    -1  &   2   &   3
\end{bmatrix}
$

일 때, 

$
AB = 
\begin{bmatrix}
    -1  &   5   &   -4  \cr
    3   &   2   &   1   
\end{bmatrix}
\begin{bmatrix}
    2   &   1   &   0   \cr
    0   &   -2  &   1   \cr
    -1  &   2   &   3
\end{bmatrix}
$

$
{=}
\begin{bmatrix}
    (-1,5,-4)\cdot(2,0,-1) & (-1,5,-4)\cdot(1, -2, 2) & (-1,5,-4)\cdot(0, 1, 3) \cr
    (3, 2, 1)\cdot(2,0,-1) & (3, 2, 1)\cdot(1, -2, 2) & (3, 2, 1)\cdot(0, 1, 3)
\end{bmatrix}
$

$
{=}
\begin{bmatrix}
    2 & -19 & -7 \cr
    5 & 1 & 5
\end{bmatrix}
$



행렬 곱 $BA$ 는 정의할 수 없음을 알 수 있다. B의 열 수와 A의 행 수가  같지 않기 때문이다.

따라서, $AB \neq BA$ 교환법칙이 성립하지 않는다.

&nbsp;<br/>

### 행렬과 벡터의 곱셈

$
\vec uA = 
\vec u
\begin{bmatrix}
    A_{11} & A_{12} & A_{13} \cr
    A_{21} & A_{22} & A_{23} \cr
    A_{31} & A_{32} & A_{33}
\end{bmatrix} =
\vec u
\begin{bmatrix}
    \uparrow & \uparrow & \uparrow \cr
    A_{\ast,1} & A_{\ast,2} & A_{\ast,3} \cr
    \downarrow & \downarrow & \downarrow
\end{bmatrix}
$

$
{=}
\begin{bmatrix}
    \vec u \cdot A_{\ast,1} & \vec u \cdot A_{\ast,2} & \vec u \cdot A_{\ast,3}
\end{bmatrix}
$

$
{=}
[ xA_{11} + yA_{21} + zA_{31}, xA_{12} + yA_{22} + zA_{32}, xA_{13} + yA_{23} + zA_{33} ]
$

$
{=}
[xA_{11},xA_{12},xA_{13}] + [yA_{21},yA_{22},yA_{23}] + [zA_{31},zA_{32},zA_{33}]
$

$
{=}
x[A_{11},A_{12},A_{13}] + y[A_{21},A_{22},A_{23}] + z[A_{31},A_{32},A_{33}]
$

$
{=}
xA_{1,\ast} + yA_{2,\ast} + zA_{3,\ast}
$

&nbsp;<br/>

# 행렬 성질

다음 같은 행렬이 있다고 하자.

$
A = \begin{bmatrix}
    1   &   5   \cr
    -2  &   3   
\end{bmatrix},
B = \begin{bmatrix}
    6   &   2   \cr
    5   &   -8  
\end{bmatrix},
C = \begin{bmatrix}
    1   &   5   \cr
    -1   &   3  
\end{bmatrix},
D = \begin{bmatrix}
    2   &   1   &   -3  \cr
    -6  &   3   &   0   
\end{bmatrix}
$

&nbsp;<br/>

## 행렬 덧셈 교환법칙

$ A + B = B + A$

&nbsp;<br/>

## 행렬 덧셈 결합법칙

$ (A + B) + C = A + (B + C) $

&nbsp;<br/>

## 스칼라 곱 분배법칙

$ k(A + B) = kA + kB$

$ (k + r)A = kA + rA$

&nbsp;<br/>

## 행렬 곱셈 교환법칙

$ CD \neq DC$

&nbsp;<br/>

## 행렬 곱셈 결합법칙

$ A(B+C) = AB+AC$

&nbsp;<br/>

## 행렬 곱셈 분배법칙

$(AB)C = A(BC)$

&nbsp;<br/>

# 전치행렬

전치(transpose) 행렬은 행렬의 행들과 열들을 바꾼 것이다. $m \times n$ 행렬의 전치는 $n \times m$ 이다.

행렬 $M$ 의 전치행렬을 $M^T$ 로 표기한다.

$
A^T = 
\begin{bmatrix}
    2 & -1 & 8 \cr
    3 & 6 & -4
\end{bmatrix}^T =
\begin{bmatrix}
    2 & 3 \cr
    -1 & 6 \cr
    8 & -4
\end{bmatrix}
$

&nbsp;<br/>

## 전치행렬의 성질

* $(A + B)^T = A^T + B^T
* $(cA)^T = cA^T$
* $(AB)^T = B^TA^T
* $(A^T)^T = A
* $(A^{-1})^T = (A^T)^{-1}$

&nbsp;<br/>

# 단위행렬

단위행렬(identity matrix) 라고 불리우는 특별한 행렬이 있다.<br/>
열 수와 행 수가 같은 정사각형의 행렬을 정방행렬(square matrix) 이라고 부른다.<br/>
정방행렬에서 좌상단에서 우하단의 대각선에 있는 성분들을 주대각(main diagonal) 성분이라고 부르는데,<br/>
단위 행렬은 주대각 성분들은 1, 그 외에는 모두 0인 정방행렬이다.

$
\begin{bmatrix}
    1 & 0 \cr
    0 & 1
\end{bmatrix} ,
\begin{bmatrix}
    1 & 0 & 0\cr
    0 & 1 & 0\cr
    0 & 0 & 1
\end{bmatrix} ,
\begin{bmatrix}
    1 & 0 & 0 & 0\cr
    0 & 1 & 0 & 0\cr
    0 & 0 & 1 & 0\cr
    0 & 0 & 0 & 1
\end{bmatrix}, ...
$

&nbsp;<br/>


## 단위 행렬의 성질

* 곱셈의 항등원 역할

$A$ 가 $m \times n$ 이고 $I$ 가 $n \times n$ 행렬이면, $AI =  A$ 이다.

* 정방행렬과 단위행렬과의 곱셈은 교환법칙을 만족한다.

$M$ 이 정방행렬이고 $I$ 가 단위행렬일 때, $MI = IM = M$ 이다.

&nbsp;<br/>


# 행렬식

행렬식(determinant)은 정방행렬을 입력받아서 실수값을 출력하는 특별한 함수이다.<br/>
정방행렬 $A$의 행렬식을 흔히 $det A$로 표기한다. <br/> 
정방행렬 $A$가 $det A \neq 0$ 일 때만, 가역행렬(역행렬이 존재하는 행렬) 이다.<br/>

&nbsp;<br/>

## 소행렬

$n \times n$행렬 $A$ 가 주어졌을 때, 그 소행렬(minor matrix) $\bar{A_{ij}}$ 는 $A$의 $i$ 행과 $j$ 열을 삭제해서 나온 $(n-1) \times $(n-1)$ 행렬이다.

&nbsp;<br/>

$
A = 
\begin{bmatrix}
    A_{11} & A_{12} & A_{13} \cr
    A_{21} & A_{22} & A_{23} \cr
    A_{31} & A_{32} & A_{33}
\end{bmatrix}
$

일 때,

$
\bar{A_{11}} = 
\begin{bmatrix}
    A_{22} & A_{23} \cr
    A_{32} & A_{33} 
\end{bmatrix}
$

$
\bar{A_{22}} = 
\begin{bmatrix}
    A_{11} & A_{13} \cr
    A_{31} & A_{33} 
\end{bmatrix}
$

$
\bar{A_{33}} = 
\begin{bmatrix}
    A_{11} & A_{12} \cr
    A_{12} & A_{22} 
\end{bmatrix}
$

이다.

&nbsp;<br/>

$A$ 가 $n \times n$ 행렬이고, $n = 1$ 일 때, A의 행렬식은 아래와 같다.

$det A = A_{11}$

$A$ 가 $n \times n$ 행렬이고, $n > 1$ 일 때, A의 행렬식은 아래와 같다.

$det A = (\sum_{j=1}^{n}A_{1j})(-1)^{1+j}det \bar{A_{1j}}$

&nbsp;<br/>

### $2 \times 2$ 행렬식

$
det
\begin{bmatrix}
    A_{11} & A_{12} \cr
    A_{21} & A_{22} 
\end{bmatrix} =
A_{11}det[A_{22}] - A_{12}det[A_{21}] = A_{11}A_{22} - A_{12}A_{21}
$

&nbsp;<br/>

### $3 \times 3$ 행렬식

$
det
\begin{bmatrix}
    A_{11} & A_{12} & A_{13}    \cr
    A_{21} & A_{22} & A_{23}    \cr
    A_{31} & A_{32} & A_{33}
\end{bmatrix}
$

$
{=}
A_{11} det
\begin{bmatrix}
    A_{22} & A_{23} \cr
    A_{32} & A_{33} 
\end{bmatrix} - 
A_{12} det
\begin{bmatrix}
    A_{21} & A_{23} \cr
    A_{31} & A_{33}
\end{bmatrix} +
A_{13} det
\begin{bmatrix}
    A_{21} & A_{22} \cr
    A_{31} & A_{32}
\end{bmatrix}
$

$
{=}
A_{11} (A_{22}A_{33} - A_{23}A_{32}) -A_{12}(A_{21}A_{33} - A_{23}A_{31}) + A_{13}(A_{21}A_{32}-A_{22}A_{31})
$

&nbsp;<br/>

### $3 \times 3$ 행렬식 풀이

$
A = 
\begin{bmatrix}
    2 & -5 & 3 \cr
    1 & 3 & 4 \cr
    -2 & 3 & 7
\end{bmatrix}
$ 

일 때,

$ detA $
$= 2(3 \cdot 7 - 4 \cdot 3 ) - (-5)(1 \cdot 7 - 4 \cdot (-2)) + 3(1 \cdot 3 - 3 \cdot (-2))$
$= 2(9)+5(15)+3(9)$
$= 18 + 75 + 27$
$= 120$

&nbsp;<br/>

# 딸림행렬

## 여인수 행렬

**

