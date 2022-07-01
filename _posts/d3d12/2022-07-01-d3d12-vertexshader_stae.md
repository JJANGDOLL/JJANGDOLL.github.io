---
title: "D3D 정점 셰이더 단계"

categories:
  - D3D12


tags:
  - d3d12
  - vertex shader

toc: true
toc_label: Vertex Shader Stage
toc_sticky: true

sidebar_main: true
---

입력 조립기 단계는 기본 도형들을 조립한 후 해당 정점들을 정점 세이더 단계로 넘겨준다. 정점 셰이더는, 정점 하나를 출력하는 함수로 생각해도 된다. 화면에 그려질 모든 정점은 이 정점 셰이더를 거쳐 간다. 개념적으로는 하드웨어 안에서 다음 같은 일이 일어난다고 볼 수 있다.

```cpp
for(UINT i=0; i < numVertices; ++i)
{
  outputVertex[i] = VertexShader(inputVertex[i]);
}
```

정점 셰이더 함수의 구체적인 내용은 프로그래머가 구현해서 GPU에 제출한다. 함수는 각 정점에 대해 GPU에서 실행되기에 매우 빠르다.

변환, 조명, 변위 매핑 등 수많은 특수 효과를 정점 셰이더에서 수행할 수 있다. 정점 셰이더에서 입력 정점 자료에 접근하는 것은 물론이고, 텍스쳐라던가 변환 행렬, 장면 광원 정보 등 GPU 메모리에 담긴 다른 자료에도 접근할 수 있다.

<br/>

# Local Space and World Space

3D 물체를 만들 떄는 전역 좌표에 직접 만드는 것이 아닌 물체 자신의 로컬 좌표계를 이용한다. 

![5_6_1](/assets/images/d3d12/5_6_1.png)

로컬 좌표계에서 잘 정렬 했다면, 월드 공간에서 적절한 위치와 방향으로 배치해야 한다. 로컬 공간을 월드 공간의 상대적인 값으로 바꾸는 것을 world transform(세계 변환) 이라고 부르고, 해당 행렬을 world matrix(세계 행렬) 이라 부른다. 장면의 모든 물체에는 각자의 world matrix 를 가진다. 각 물체를 자신의 로컬공간에서 월드 공간으로 변환하고 나면 모든 물체 좌표가 동일한 좌표계(월드 공간)에 상대적인 좌표가 된다. 만일 물체를 월드 공간 안에서 직접 정의하고 싶다면, 그 물체의 world matrix 를 단위 행렬로 설정하면 된다.

각 모형을 로컬 공간으로 정의하는 데에는 여러 장점이 있다.

1. 더 쉽다. 로컬 공간은 나의 중심이 중점이라 그렇지 않은 월드공간보다 정의하기가 훨씬 쉽다.

![5_6_1_2](/assets/images/d3d12/5_6_1_2.png)

2. 한 물체가 여러 장면에 재사용될 수 있는데, 그런 경우 물체의 좌표들을 특정 장면 기준으로 고정시키는 것은 비합리적이다.
3. 한 물체를 하나의 장면 안에 위치나 방향, 비례를 달리해서 여러번 그리는 경우가 많다. 그 인스턴스마다 물체의 정점 및 색인자료를 들고 있으면 자원 낭비가 심하다. 차라리 로컬 공간 기준의 한 오브젝트를 저장해두고 월드 공간에서 위치와 방향, 비례를 정의하는 world matrix 를 다르게 설정하는 것이 효율적이다. 이러한 기법을 인스턴싱 이라고 부른다.

물체의 월드 공간을 세계 공간에 상대적인 좌표들로 서술하고 그 좌표들을 행들로 사용해서 하나의 행렬로 만들면 그 물체의 world matrix 가 된다. $Q_w = (q_x, q_y, q_z, 1), U_w = (u_z, u_y, u_z, 0), V_w = (v_z, v_y, v_z), W_w = (w_x, w_y, w_z)$ 이 각각 로컬 공간의 원점과 x, y, z 축들을 월드 공간으로 상대적인 동차좌표로 나타낸 것이라고 할때, 로컬 공간을 월드 공간으로 변환하는 좌표 변경 행렬은 아래와 같다.

$
\begin{bmatrix}
    u_x & u_y & u_z & 0 \cr
    v_x & v_y & v_z & 0 \cr
    w_x & w_y & w_z & 0 \cr
    q_x & q_y & q_z & 1 \cr
\end{bmatrix}
$

이런 world matrix 를 알려면 로컬 공간 원점 및 축들의 월드 공가넹 상대적인 좌표를 알아야만 한다. 근데 알아내기가 어렵기에 좀 더 흔한 접근 방식은 $W$ 를 일련의 변환들의 형태로 정의하는 것이다. 예를 들어 $W = SRT$ 로 둘 수 있다. 물체를 월드 공간 안에서 적절한 크기가 되게 만드는 비례행렬 S에 로컬 공간의 물체의 방향을 세계 공간에 상대적인 방향으로 바꾸는 회전 행렬 R을 곱하고, 로컬 공간에서 물체의 원점을 세계 공간의 상대적인 위치로 변화시키는 이동 행렬 T를 곱한 것을 world matrix 로 사용한다. 이러한 일련의 변환들을 하나의 좌표 변경 변환으로 해석할 수 있으며, $W = SRT$ 의 행벡터들은 로컬 공간의 x, y, z 축과 원점의 세계 공간에 상대적인 동차 좌표들을 담는다.

<br/>

## 예시

로컬 공간 기준으로 두 점 $(-0.5, 0, -0.5), (0.5, 0, 0.5)$ 으로 나타낼 수 있는 길이 1인 정사각형이 있다고 하자. 이 정사각형을 월드 공간에서 한 변의 길이가 2이고, xz 평면에서 시계방향으로 45도 회전한 방향을 가르키며 중심으로부터 $(10, 0, 10)$ 위치에 놓인 정사각형으로 변환한다고 하자.

우선. 개별 변환 $S, R, T$ 를 정의하고 그것들을 모두 곱해 $W$를 만든다.

$ 
S =
\begin{bmatrix}
  2 & 0 & 0 & 0 \cr
  0 & 1 & 0 & 0 \cr
  0 & 0 & 2 & 0 \cr
  0 & 0 & 0 & 1 \cr
\end{bmatrix}, 
R = 
\begin{bmatrix}
  \frac{\sqrt{2}}{2} & 0 & -\frac{\sqrt{2}}{2} & 0 \cr
  0 & 1 & 0 & 0 \cr
  \frac{\sqrt{2}}{2} & 0 & \frac{\sqrt{2}}{2} & 0 \cr
  0 & 0 & 0 & 1 \cr
\end{bmatrix},
T = 
\begin{bmatrix}
  1 & 0 & 0 & 0 \cr
  0 & 1 & 0 & 0 \cr
  0 & 0 & 1 & 0 \cr
  10 & 0 & 10 & 1 \cr
\end{bmatrix}
$

$
W = 
SRT = 
\begin{bmatrix}
  \sqrt{2} & 0 & -\sqrt{2} & 0 \cr
  0 & 1 & 0 & 0 \cr
  \sqrt{2} & 0 & \sqrt{2} & 0 \cr
  10 & 0 & 10 & 1 \cr
\end{bmatrix}
$

위 $W$ 는 로컬 공가느이 좌표 축들과 원점을 월드 공간에 상대적으로 나타낸 것이다. 이 $W$ 를 이용해서 월드 공간 좌표들을 월드 공간 좌표로 변환하면 우리가 원했던 형태로 월드 공간에 배치된다.

$
\begin{bmatrix}
  -0.5 & 0 & -0.5 & 1
\end{bmatrix}
W = 
\begin{bmatrix}
  10-\sqrt{2} & 0 & 0 & 1
\end{bmatrix}
$

$
\begin{bmatrix}
  -0.5 & 0 & +0.5 & 1
\end{bmatrix}
W = 
\begin{bmatrix}
  0 & 0 & 10+\sqrt{2} & 1
\end{bmatrix}
$

$
\begin{bmatrix}
  +0.5 & 0 & +0.5 & 1
\end{bmatrix}
W = 
\begin{bmatrix}
  10+\sqrt{2} & 0 & 0 & 1
\end{bmatrix}
$

$
\begin{bmatrix}
  +0.5 & 0 & -0.5 & 1
\end{bmatrix}
W = 
\begin{bmatrix}
  0 & 0 & 10-\sqrt{2} & 1
\end{bmatrix}
$

즉, 변환들을 조합해서 world matrix 를 얻을 수 있다는 것에 초점을 맞추자.

<br/>

# View space

3차원 공간의 2차원 이미지를 만드려면 가상의 카메라를 배치해야 한다. 그 카메라는 세계에서 관찰자에게 보이는 영역을 결정한다. 그 카메라가 보이는 영역을 2차원 이미지로 만들어 화면에 표시할 영역이다. 그러한 가상 카메라에 로컬 좌표계를 부여한다고 하자. 이 좌표계는 eye space(시점 공간) 이나 camera space(카메라 공간)이라고도 하는 view space(시야 공간) 을 정의한다. 카메라는 이 시야 공간의 원점에 놓여서 양의 z축(전방)을 바라본다. 여기서 x는 카메라의 오른쪽, y는 위쪽이다. 렌더링 파이프라인의 후반부 단게들에서는 월드 공간이 아닌 시야 공간 기준으로 서술하는 것이 편한 경우가 있다. 이처럼 월드 공간에서 시야 공간으로의 좌표 변경 변환을 view transform(시야 변환)이라 부르며, 해당 변환 행렬을 view matrix(시야 행렬) 이라고 부른다.


![5_6_2_1](/assets/images/d3d12/5_6_2_1.png)

로컬 공간 원점 및 x, y, z 축의 월드 공간의 상대적인 동차좌표들이 $Q_w = (Q_x, Q_y, Q_z, 1), U_w = (U_x, U_y, U_z, 0), V_w = (V_x, V_y, V_z, 0), W_w = (W_x, W_y, W_z, 0)$ 이라고 하자. 그렇다면 시야 공간에서 월드 공간으로의 좌표 변경 행렬은 아래와 같다.

$
W = 
\begin{bmatrix}
  U_x & U_y & U_z & 0 \cr
  V_x & V_y & V_z & 0 \cr
  W_x & W_y & W_z & 0 \cr
  Q_x & Q_y & Q_z & 1 \cr
\end{bmatrix}
$

근데 이건 시야공간을 월드 공간으로 만드는 것이다. 우린 그 반대 방향의 변환, 즉 월드 공간에서 시야 공간으로의 변환을 원한다. 어떤 변환의 역변환을 위한 행렬은 역행렬이다. 따라서, 월드 공간에서 시야 공간으로의 변환 행렬은 $W^{-1}$ 이다.

일반적으로 월드 좌표계와 시야 좌표계는 위치와 방향만 다르기에 $W = RT$ 라고 할 수 있다. world matrix 를 이처럼 회전 행렬과 이동 행렬로 분해하면 역행렬을 구하기 쉬워진다.

$
V = W^{-1} = (RT)^{-1} = T^{-1}R^{-1} = T^{-1}R^{T}
$

$ =
\begin{bmatrix}
  1 & 0 & 0 & 0 \cr
  0 & 1 & 0 & 0 \cr
  0 & 0 & 1 & 0 \cr
  -Q_x & -Q_y & -Q_z & 1 \cr
\end{bmatrix}
\begin{bmatrix}
  u_x  & v_x & w_x & 0 \cr
  u_y  & v_y & w_y & 0 \cr
  u_z  & v_z & w_z & 0 \cr
  0  & 0 & 0 & 1 \cr
\end{bmatrix} =
\begin{bmatrix}
  u_x  & v_x & w_x & 0 \cr
  u_y  & v_y & w_y & 0 \cr
  u_z  & v_z & w_z & 0 \cr
  -Q \cdot U  & -Q \cdot V & -Q \cdot W & 1 \cr
\end{bmatrix}
$

시야 행렬 V는 결국,

$
\therefore V =
\begin{bmatrix}
  u_x  & v_x & w_x & 0 \cr
  u_y  & v_y & w_y & 0 \cr
  u_z  & v_z & w_z & 0 \cr
  -Q \cdot U  & -Q \cdot V & -Q \cdot W & 1 \cr
\end{bmatrix}
$

로 정의된다.

![5_6_2_2](/assets/images/d3d12/5_6_2_2.png)


시야 행렬을 구축하는 데 필요한 벡터들을 직관적으로 구하는 방법을 알아보자. Q가 카메라의 위치이고 T가 카메라가 바라보는 지점 (Target Point : 대상점) 이라고 하자. j 는 월드 공간의 위쪽을 가르키는 단위벡터, 즉 up vector 라고 하자. 이 때 카메라가 바라보는 방향은 아래와 같다

$ w =
\frac{T-Q}{||T-Q||}
$

이 벡터는 카메라의 로컬 z축에 해당한다. 그렇다면 W의 우측의 단위 벡터는 아래와 같다.

$ u =
\frac{j \times w}{|| j \times w ||}
$

이 벡터는 카메라의 로컬 x축에 해당한다. 마지막으로 카메라 로컬 y 벡터는 아래와 같다.

$ v = w \times u $

카메라의 위치와 대상점, 그리고 월드 업 벡터만 있으면 카메라를 서술하는 로컬 좌표계를 유도할 수 있으며 그것을 이용해서 시야 행렬을 구할 수 있다.

DirectXMath 라이브러리는 방금 설명한 절차를 따라서 시야 행렬을 계산하는 함수를 제공한다.

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixLookAtLH(
  [in] FXMVECTOR EyePosition,
  [in] FXMVECTOR FocusPosition,
  [in] FXMVECTOR UpDirection
);
```

일반적으로 월드 공간의 y축이 장면의 '위쪽' 방향에 해당하며, 그런 경우 보통의 카메라 설정에서 상향 벡터는 $ j = (0, 1, 0)$ 이다. 예시로 카메라를 월드 공간의 $(5, 3, -10)$ 에 두고 월드 공간의 원점 $(0, 0, 0)$ 를 바라보게 한다고 할 때 시야행렬을 구하는 코드는 아래와 같다.

```cpp
XMVECTOR pos = XMVectorSet(5, 3, -10, 1.0f);
XMVECTOR target = XMVectorZero();
XMVECTOR up = XMVectorSet(0.0f, 1.0f, 0.0f, 0.0f);

XMMATRIX V = XMMaxtirxLookAtLH(pos, target, up);
```

<br/>

# Projection and Homogeneous Clip Space

카메라를 서술하는 요소가 하나 더 있다. 바로 카메라가 보이는 공간이다. 그 공간은 하나의 절두체(끝이 잘린 사각뿔)로 정의된다.

3차원 장면을 2차원 이미지로 표현하려면 절두체 안에 있는 3차원 기하 구조를 2차원 투영창으로 투영해야 한다. 3차원의 환상을 만들어 내려면 그러한 투영(projection)을 반드시 평행선들이 하나의 소실점으로 수렴하는 방식으로 수행해야 한다. 원근 투영이 바로 그런 방식이다.

![5_6_3_1](/assets/images/d3d12/5_6_3_1.png)

3차원 기하구조의 한 정점에서 시점(eye point) 으로의 직선을 정점의 투영선(point of projection) 이라고 부른다. 원근 투영 변환은 하나의 3차원 정점 v를 투영선이 2차원 투영 평면과 만나는 점 $v'$ 변환하는 변환이다. 그러한 점 $v'$ 를 $v$의 투영 이라고 한다. 그리고 3차원 물체의 투영은 그 물체를 구성하는 모든 정점의 투영을 뜻한다.

![5_6_3_2](/assets/images/d3d12/5_6_3_2.png)

<br/>

## 절두체 정의

시야 공간에서 투영의 중심을 원점에 두고 양의 z축을 바라보는 시야 절두체를 네 가지 수량을 이용해서 정의할 수 있다. 원점과 가까운 평면 사이의 거리 n, 먼 평면 사이의 거리 f, 수직 시야각 a, 종횡비 r 이다. 시야 공간에서 가까운 평면과 먼 평면이 xy 평면과 평행하다는 것을 주목하자. 따라서, 원점과 가까운/먼 평면 사이의 거리들을 그냥 z 축 상의 거리들로 지정할 수 있다. 종횡비(aspect ratio)는 $r = w/h$ 로 정의되는데, 여기서 w는 투영 창(projection window) 너비이고 h 는 투영 창의 높이이다. 투영 창은 본질적으로 2차원 이미지이다. 이 이미지가 결국에는 후면 버퍼에 매칭되기에 투영 창의 종횡비를 후면 버퍼의 종횡비와 일치시키는 것이 바람직하다. 그래서 일반적으로 투영 창의 종횡비는 후면 버퍼의 너비와 높이에 맞게 설정한다. 예를 들어 후면버퍼가 $800 \times 600$ 이면 투영 창의 종횡비는 $r = \frac{800}{600} \approx 1.333$ 으로 설정한다. 투영창의 종횡비와 후면버퍼의 종횡비가 다르면 후면 버퍼에 매칭할 때 비균 등 비레를 적용해야 하는데 그러면 이미지가 왜곡된다.

수평 시야각은 $\beta$ 로 표기한다. 이 시야각은 수직 시야각 $\alpha$ 와 종횡비 $r$ 로 결정된다. $\beta$ 를 구하는데 $r$이 어떻게 도우되는지를 아래에서 보자.

![5_6_3_3](/assets/images/d3d12/5_6_3_3.png)

종횡비는 이미 결정된 값이기에 바꿀 수 없지만 투영 창의 실제 크기(너비와 높이)는 우리가 임의로 선택할 수 있다. 따라서 거리하기 쉽도록 투영창의 높이를 2로 두자. 그러면 너비는 아래처럼 주어진다.

$ r =
\frac{w}{h} = \frac{w}{2} \Rightarrow w = 2r
$

수직 시야각이 $\alpha$ 가 되는 투영 창의 거리 $d$ 는 아래와 같다.

$
\tan{(\frac{\alpha}{2})} = \frac{1}{d} \Rightarrow d = \cot{ ( \frac{\alpha}{2} )}
$

투영 창의 높이가 2 일때 수직 시야각 $\alpha$ 가 되는 투영창 거리 $d$를 구했다. 이제 수평 시야각 $\beta$ 를 구해보자.

$
\tan{(\frac{\beta}{2})} = \frac{r}{d} = \frac{r}{\cot{(\frac{\alpha}{2})}} = r \cdot \tan{\frac{\alpha}{2}}
$

정리하면 투영 창의 높이가 2 일때 수직 시야각 $\alpha$와 종횡비 $r$ 로부터 수평 시야각 $\beta$ 를 구하는 다음과 같은 공식이 나온다.

$
\beta = 2 \tan{(r \cdot \tan{\frac{\alpha}{2}})}^{-1}
$

<br/>

# 정점의 투영

![5_6_3_4](/assets/images/d3d12/5_6_3_4.png)

위 그림의 $(x, y, z)$ 를 $z = d$ 평면에 투영한 점 $(x', y", z')$ 를 구하고자 한다. 좌표 성분 x와 y를 따로 고찰하고 닮은 꼴 삼각형의 원리를 적용하면

$
\frac{x'}{d} = \frac{x}{z} \Rightarrow x' = \frac{xd}{z} = \frac{x\cot(a/2)}{z} = \frac{x}{z\tan(a/2)}
$

이며

$
\frac{y'}{d} = \frac{y}{z} = \Rightarrow y' = \frac{yd}{z} = \frac{y \cot(a/2)}{z} = \frac{y}{z \tan(a/2)}
$

임을 알 수 있으며 $(x, y, z)$가 절두체 안에 있을 필요충분조건은 아래와 같다.

$
-r \leq x' \leq r,
-1 \leq y' \leq 1,
n \leq z \leq f
$

<br/>

## Normalized Device Coordinates

앞에서는 투영된 점의 좌표를 시야 공간에서 계산했다. 시야 공가넹서 투영 창의 높이가 2이고 너비가 2r 이다. 이 방식의 문제점은 크기가 종횡비에 의존한다는 점이다. 나중에 하드웨어가 투영 창의 크기에 관련된 연산들을 수행할 때 종횡비를 알아야 하므로, 응용 프로그램이 종횡비를 하드웨어에게 알려주어야 한다. 만약 종횡비의 의존성을 없앤다면 작업이 더 수월해 질것이다. 해결 책은, 투영된 점의 x 성분을 다음과 같이 $[-r, r]$ 구간에서 $[-1, 1]$ 으로 비례하는 것이다.

$
-r \leq x' \leq r
$
$
-1 \leq x' / \r \leq 1
$

x, y 성분을 이렇게 매핑한 후 좌표를 normalize device coordinates(정규화된 장치 좌표,NDC) 라고 부른다(z 성분은 아직 정규화 되지 않았다). 이 경우 $(x, y, z)$ 가 절두체 안에 있을 필요충분조건은 아래와 같다.

$
-1 \leq x' / \r \leq 1
$

$
-1 \leq y' \leq 1
$

$
n \leq z \leq f
$

시야 공간에서 NDC 공간으로 변환을 일종의 단위 변환(unit conversion)으로 볼 수도 있다. x 축에서 NdC 의 한 단위는 시야 공간의 r 단위와 같다(즉, $1 ndc = r vs$). 따라서 시야 공간의 x 단위를 NDC 단위로 변환하고 싶다면 다음 공식을 사용하면 된다.

$ 
xvs \cdot \frac{1ndc}{rvs} = \frac{x}{r}ndc
$

이제 투영 공식들을 다음과 같이 변경한다면, 투영된 x와 y의 NDC 좌표성분들을 직접 얻을 수 있다.

$
x' = \frac{x}{rz \tan(a/2)}
$

$
y' = \frac{x}{z \tan(a/2)}
$

NDC 좌표에서는 투영 창의 높이가 2고 너비도 2다. 투영 창의 크기가 고정되었으므로 하드웨어는 종횡비를 몰라도 된다. 대신, 실제로 NDC 공간을 기준으로 한 투영 좌표들을 공급하는 것은 이제 프로그래머의 몫이다.

<br/>

## 투영 변환을 행렬로 표현

일관성을 위해 투영 변환을 하나의 행렬로 표현하는 것이 바람직하다. 투영은 z성분도 정규화된다. 이는 투영 변환 단계에서 나누기에 사용할 원래의 z 성분이 더 이상 남아있지 않음을 뜻한다. 따라서 반드시 투영 변환 전에 입력 z 성분을 어딘가에 저장해 두어야 한다. 이를 위해, 동차 좌표의 w 성분에 입력 z 성분을 복사해준다. 행렬 곱셈 관점에서는 이는 $[2][3]$ 성분을 1로, $[3][3]$ 성분을 0으로 두는 것에 해당한다. 투영 행렬은 다음과 같은 모습니다.

$
\begin{bmatrix}
  \frac{1}{r \tan(a/2) } & 0 & 0 & 0 \cr
  0 & \frac{1}{ \tan(a/2) } & 0 & 0 \cr
  0 & 0 & A & 1 \cr
  0 & 0 & B & 0 \cr
\end{bmatrix}
$

이 행렬에 상수 A, B 가 배치되어 있음을 주목하자. 이 상수들은 입력 z 좌표를 정규화된 구간 $[-1, 1]$로 변환하는데 쓰인다. 임의의 점 $(x, y, z, 1)$ 에 이 행렬을 곱하면 다음이 나온다.

$
\begin{bmatrix}
  x & y & z & 1
\end{bmatrix}
\begin{bmatrix}
  \frac{1}{r \tan(a/2) } & 0 & 0 & 0 \cr
  0 & \frac{1}{ \tan(a/2) } & 0 & 0 \cr
  0 & 0 & A & 1 \cr
  0 & 0 & B & 0 \cr
\end{bmatrix}
$

$ =
\begin{bmatrix}
  \frac{x}{r \tan(a/2)} & \frac{y}{\tan(a/2)} & Az + B & z
\end{bmatrix}
$

이처럼 투영 행렬을 곱하는 것이 전체 투영 변환의 선형 부분이다. 이제 비선형 부분을 적용하면, 다시 말해 각 좌표 성분을 $w=z$  로 나누면 투영 변환이 완성된다.

$
\begin{bmatrix}
  \frac{x}{r \tan(a/2)} & \frac{y}{\tan(a/2)} & Az + B & z
\end{bmatrix}
\xrightarrow{w로 나누기}
\begin{bmatrix}
  \frac{x}{rz \tan(a/2)} & \frac{y}{z \tan(a/2)} & Az + \frac{B}{z} & 1
\end{bmatrix}
$

0으로 나누기를 걱정할 수도 있지만, 가까운 평면과의 거리는 반드시 0보다 커야하며, z성분이 0인 점은 시야 절두체 밖에 있다. 그런 점은 이미 절단된 이후여서 투영 변환의 대상이 아닝다. w로 나누기를 perspective divide나 homogeneous divide 라고 부르기도 한다. 

<br/>

## 정규화된 깊이 값

투영이 끝났다면 투영된 점들이 모두 2차원 투영창에 놓여서 관찰자에게 보이는 2차원 이미지를 형성할 것이므로, 원래의 z 성분은 폐기해도 될 것이라고 생각할 수 있지만, 깊이 버퍼링 알고리즘을 위해 3차원 깊이 정보가 여전히 필요하다. 투영된 성분 x, y 성분을 일정 구간으로 정규화하는 것과 마찬가지로 D3D는 깊이 성분도 일정 구간으로 정규화한다. 단, 깊이 성분의 정규화 구간은 $[0, 1]$ 이다. 이러한 정규화를 위해선 구간 $[n, f]$ 를 $[0,1]$로 매핑하는 함수 g(z) 가 필요하다. 그러한 함수는 순서를 보존하는 함수여야 한다. 순서를 보존한다는 의미는 $z_1, z_2 \in [n, f]$ 일 때, 만일 $z_1 \lt z_2$ 라면 $g(z_1) \lt g(z_2)$ 이여야 한다는 것이다. 이런 함수들로 깊이 값들을 변환하면 깊이 값들의 상대적인 관계가 유지된다. 따라서 정규화된 구간의 깊이들을 비교해도 결과가 정확하다.

$[n, f]$ 비례 연산 1회와 이동 연산 1회를 통해서 $[0, 1]$로 매핑할 수 있다. 근데 그러한 접근 방식은 현재의 투영 전략과 잘 통합되지 않는다. perspective deivide 로 인해 z 성분은 다음과 같은 변환을 거친다.

$
g(z) = A + \frac{B}{z}
$

이제 다음 두 구속조건을 만족하는 A와 B를 선택해야 한다.

* $g(n) = A + B/n = 0$ (가까운 평면이 0으로 매핑됨)
* $g(f) = A + B/f = 1$ (먼 평면이 1로 매핑됨)

조건 1를 B에 대해 정리하면 $B = -An$ 이 나온다. 이를 조건 2에 대입해서 A에 대해 정리하면 아래와 같이 나온다.

$
A = \frac{f}{f - n}
$

$
\therefore g(z) = \frac{f}{f - n} - \frac{nf}{(f-n)z}
$

이다.

g의 그래프는 이 함수가 순증가 함수이다 비선형 함수라는 것을 보여준다. 순증가 함수는 곧 순서보존 함수이다.

![5_6_3_5](/assets/images/d3d12/5_6_3_5.png)

그래프는 도한 가까운 평면에 근접한 구간의 깊이 값들이 대부분을 '차지한다' 라는 점도 보여준다. 즉, 깊이 값들의 대부분이 치역의 작은 부분 집합에 몰려있는 것이다. 이는 깊이 버퍼 정밀도 문제로 이어진다. 이에대한 일반적인 조언은, 가까운 평면과 먼 평면읠 최대한 가깝헤 해서 깊이 정밀도 문제를 최소화 하라는 것이다.

이제 앞에서 구한 A, B로 원근투영 행렬을 완성하면 다음이 나온다.

$ P =
\begin{bmatrix}
  \frac{1}{r \tan(a/2)} & 0 & 0 & 0 \cr
  0 & \frac{1}{tan(a/2)} & 0 & 0 \cr
  0 & 0 & \frac{f}{f-n} & 1 \cr
  0 & 0 & \frac{-nf}{f-n} & 0 \cr
\end{bmatrix}
$

이 투영 행렬을 곱한 후의, 하지만 원근 나누기는 아직 수행하기 전의 기하구조를 가리켜 homogeneous clip space 또는 projection space 라고 말한다. perspective divide 를 수행한 후의 기하구조를 가리켜 NDC 공간에 있다고 말한다.

<br/>

## XMMatrixPerspectiveForLH 함수

DirectXMath 라이브러리의 다음 함수를 이용해서 원근투영행렬을 구축할 수 있다.

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixPerspectiveFovLH(
  [in] float FovAngleY,
  [in] float AspectRatio,
  [in] float NearZ,
  [in] float FarZ
);
```

다음은 이 함수의 사용법을 보여주는 예제이다. 수직 시야각을 45도, 가까운 평면은 $z=1$, 먼 평면은 $z= 1000$에 둔다(시야 공간 기준이다).

```cpp
float D3DApp::AspectRatio() const
{
  return static_cast<float>(mClientWidth) / mClientHeight;
}

XMMATRIX P = XMMatrixPerspectiveForLH(0.25f * XM_PI, AspectRatio(), 1.0f, 1000.0f);
```


