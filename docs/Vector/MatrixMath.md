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

$$
A =
\begin{bmatrix}
3.5 & 0 & 0 & 0\\ 
 0  & 1 & 0 & 0 \\ 
 0&  0& 0.5 &0 \\ 
 2& -5 & \sqrt{2} & 1
\end{bmatrix},
 B = 
\begin{bmatrix}
   B_{11} & B_{12} \\
   B_{21} & B_{22} \\
   B_{31} & B_{32} \\
   B_{41} & B_{42} \\
\end{bmatrix} \;
u = \begin{bmatrix}
    u_{1} & u_{2} &  u_{3} \\
\end{bmatrix} \;
v = \begin{bmatrix}
    1 \\
    2 \\
    \sqrt{3} \\
    \pi \\
\end{bmatrix} \;
$$

* 행렬 A는 $4 \times 4$ 행렬이고, B는 $3 \times 2$, u는 $1 \times 3$, v는 $4 \times 1$ 이다.
* A의 4행 2열의 성분 (-5) 는 $A_{42}$ 로 표현한다.
* $u, v$는 행이나 열이 하나라는 점에서 특별한 행렬이다. 이것을 행벡터, 열벡터 라고 부른다.
  * 이들은 벡터를 행렬 형태로 표기할 때 쓰인다.

종종 한 행렬의 행을 한 번에 표기하는 법이 편리할 때가 있다. 

$$
\begin{bmatrix}
   A_{11} & A_{12} & A_{13}\\
   A_{21} & A_{22} & A_{23}\\
   A_{31} & A_{32} & A_{33}\\
\end{bmatrix} =
\begin{bmatrix}
    \leftarrow  A_{1,*} \rightarrow \\
    \leftarrow  A_{2,*} \rightarrow \\
    \leftarrow  A_{3,*} \rightarrow \\
\end{bmatrix}
$$

여기서 $A_{1,*} = [A_{11}, A_{12}, A_{13}]$ 를 의미한다.<br/>

마찬가지로, 열 벡터들도 아래처럼 표현할 수 있다.

$$
\begin{bmatrix}
   A_{11} & A_{12} & A_{13}\\
   A_{21} & A_{22} & A_{23}\\
   A_{31} & A_{32} & A_{33}\\
\end{bmatrix} =
\begin{bmatrix}
    \uparrow & \uparrow&\uparrow\\
    A_{*,1} &A_{*,2}&A_{*,3} \\
    \downarrow & \downarrow&\downarrow\\
\end{bmatrix}
$$

여기서,

$$ A_{*,1} =
\begin{bmatrix}
   A_{11}\\
   A_{21}\\
   A_{31}\\
\end{bmatrix}
$$

를 의미한다.

&nbsp;<br/>

# 행렬 연산

다음 같은 행렬이 있다고 하자.

$$
A = \begin{bmatrix}
    1   &   5   \\
    -2  &   3   \\
\end{bmatrix},
B = \begin{bmatrix}
    6   &   2   \\
    5   &   -8  \\
\end{bmatrix},
C = \begin{bmatrix}
    1   &   5   \\
    -1   &   3  \\
\end{bmatrix},
D = \begin{bmatrix}
    2   &   1   &   -3  \\
    -6  &   3   &   0   \\
\end{bmatrix}
$$

&nbsp;<br/>

## 덧셈




&nbsp;<br/>