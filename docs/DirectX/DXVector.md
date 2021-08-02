---
layout: default
title: "DirectX Vector"
nav_order: 1
parent: "[GameDev] DirectX"
use_math: true
---

# DirectXMath 라이브러리 의 벡터
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# DirectXMath 란?

Win8 이상에서 Direct3D 응용 프로그램을 위한 표준적인 3차원 수학 라이브러리이다.

Windows SDK 일부인 이 라이브러리는 [SSE2](https://ko.wikipedia.org/wiki/SSE2) (Streaming SIMD Extensions 2) 명령 집합을 활용한다.

[SIMD](https://ko.wikipedia.org/wiki/SIMD) 명령어들은 128 bit 너비의 SIMD(Single instruction multiple data) 레지스터들을 이용해서 32 bit float 또는 int 네 개를 한 명령에서 단번에 처리가 가능하다.

이것은 벡터 연산에 매우 유용하다.

&nbsp;<br/>

## 사용 방법

* DirectXMath.h 를 포함시켜야 한다.
* DirectXMath 코드는 DirectX 라는 namespace 에 속한다.
* DirectXPackedVector.h 를 포함시켜야 한다.
* DirectXPackedVector 코드는 DirectX::PackedVector 라는 namespace 에 속한다.
* x86 인 경우 활성화 해야한다.(프로젝트 속성 > 구성 속성 > C/C++ > 코드 생성 > 고급 명령 집합 사용)
* x64 인 경우 기본 지원한다.

&nbsp;<br/>

## 형식

핵심 벡터 형식은 SIMD 하드웨어 레지스터에 대응되는 **XMVECTOR** 이다.

이 128bit 크기의 형식은 32 비트 부동소수점 값 네 개로 구성되는데, SIMD 명령 하나로 네 값을 한꺼번에 처리가 가능하다.

x64 나 SSE가 활성화된 x86 에서는 아래와 같이 정의되어있다.

```
typedef __m128 XMVECTOR;
```

__m128 은 특별한 SIMD 형식이다. 벡터 계산 시에 SIMD 장점이 발휘되려면 벡터가 반드시 이 형식이여야 한다.

여기서 XMVECTOR 는 16 바이트 경계예 alignment 가 되는데, 지역 변수와 전역 변수에서는 자동으로 일어나버린다.

따라서 클래스 자료 멤버에는 XMVECTOR 대신 XMFLOAT2, XMFLOAT3, XMFLOAT4 를 사용하는 것을 권장한다.

하지만 이 형식들을 계산에 사용하게 되면 SIMD의 장점을 취할 수 없다. 그래서 SIMD를 활용하려면 XMVECTOR 형식으로 변환해야 한다.

이것을 위해 DirectXMath 는 변환을 수행하는 load(적재) 함수를 제공한다. 마찬가지로 XMVECTOR 를 XFLOAT으로 변환하는 store(저장) 함수도 제공한다.

&nbsp;<br/>

## 정리

1. 지역변수, 전역변수는 XMVECTOR 사용
2. 클래스 자료 멤버에는 XMFLOAT<sub>n</sub> 사용
3. 계산 전에 XMVECTOR 로 변환
4. XMVECTOR 형태로 계산
5. XMVECTOR 를 XMFLOAT<sub>n</sub> 로 변환

&nbsp;<br/>

# Load, Store 함수

[Docs: Functions of load and store](https://docs.microsoft.com/en-us/windows/win32/api/directxmath/nf-directxmath-xmloadfloat)

## Load

{% highlight markdown %}
```C++
XMVECTOR XM_CALLCONV  noexcept XMLoadFloat2(
  const XMFLOAT2 *pSource
);
```
{% endhighlight %}

```C++
XMVECTOR XM_CALLCONV  noexcept XMLoadFloat3(
  const XMFLOAT3 *pSource
);
```

```C++
XMVECTOR XM_CALLCONV  noexcept XMLoadFloat4(
  const XMFLOAT4 *pSource
);
```

&nbsp;<br/>

## Store

```C++
void XM_CALLCONV  noexcept XMStoreFloat2(
  XMFLOAT2  *pDestination,
  FXMVECTOR V
);
```

```C++
void XM_CALLCONV  noexcept XMStoreFloat3(
  XMFLOAT3  *pDestination,
  FXMVECTOR V
);
```

```C++
void XM_CALLCONV  noexcept XMStoreFloat4(
  XMFLOAT4  *pDestination,
  FXMVECTOR V
);
```

&nbsp;<br/>

## Read/Write Custom Component

```C++
float XM_CALLCONV  noexcept XMVectorGetX(
  FXMVECTOR V
);
float XM_CALLCONV  noexcept XMVectorGetY(
  FXMVECTOR V
);
float XM_CALLCONV  noexcept XMVectorGetZ(
  FXMVECTOR V
);
float XM_CALLCONV  noexcept XMVectorGetW(
  FXMVECTOR V
);

XMVECTOR XM_CALLCONV  noexcept XMVectorSetX(
  FXMVECTOR V,
  float     x
);
XMVECTOR XM_CALLCONV  noexcept XMVectorSetY(
  FXMVECTOR V,
  float     y
);
XMVECTOR XM_CALLCONV  noexcept XMVectorSetZ(
  FXMVECTOR V,
  float     z
);
XMVECTOR XM_CALLCONV  noexcept XMVectorSetW(
  FXMVECTOR V,
  float     w
);
```

&nbsp;<br/>

# XVECTOR 사용방법

## 파라메타 전달

[Docs: Send Parameters](https://docs.microsoft.com/en-us/windows/win32/dxmath/pg-xnamath-internals)

XMVECTOR 를 인자로 전달할 때, 효율성을 위해 XMVECTOR 값이 스택이 아닌 SSE/SSE2 레지스터로 전달되어야한다.

인수의 갯수는 플랫폼, 컴파일러에 따라 다르므로 이런 의존성을 없애기 위해 다른 형식을 써주어야 한다.

또, SSE/SSE2 레지스터 활용을 위한 호출 규약 역시 의존성을 없애려면 XM_CALLCONV 라는 지시자를 붙여야 한다.

* FXMVECTOR : 처음 세 인자 형식
* GXMVECTOR : 네 번째 인자 형식
* HXMVECTOR : 다섯째, 여섯째 인자 형식
* CXMVECTOR : 그 이후 인자 형식

```C++
example)

XMMATRIX XM_CALLCONV  noexcept XMMatrixTransformation(
  FXMVECTOR ScalingOrigin,
  FXMVECTOR ScalingOrientationQuaternion,
  FXMVECTOR Scaling,
  GXMVECTOR RotationOrigin,
  HXMVECTOR RotationQuaternion,
  HXMVECTOR Translation
);

XMMATRIX XM_CALLCONV  noexcept XMMatrixTransformation2D(
  FXMVECTOR ScalingOrigin,
  float     ScalingOrientation,
  FXMVECTOR Scaling,
  FXMVECTOR RotationOrigin,
  float     Rotation,
  GXMVECTOR Translation
);
```

&nbsp;<br/>

## 상수화

const XMVECTOR 형태에는 반드시 XMVECTOR32 형식을 사용해야 한다.

```C++
example)

static const XMVECTORF32 g_vFLTMAX = { FLT_MAX, FLT_MAX, FLT_MAX, FLT_MAX };
static const XMVECTORF32 g_vFLTMIN = { -FLT_MAX, -FLT_MAX, -FLT_MAX, -FLT_MAX };
static const XMVECTORF32 g_vHalfVector = { 0.5f, 0.5f, 0.5f, 0.5f };
static const XMVECTORF32 g_vMultiplySetzwToZero = { 1.0f, 1.0f, 0.0f, 0.0f };
static const XMVECTORF32 g_vZero = { 0.0f, 0.0f, 0.0f, 0.0f };
```

정수 자료를 담은 상수 XMVECTOR 를 생성하고 싶다면 [XMVECTORU32](https://docs.microsoft.com/ko-kr/windows/win32/dxmath/ovw-xmvectoru32-operators) 를 사용하자.

&nbsp;<br/>

## XMVECTOR Initialization

DirectXMath 라이브러리는 XMVECTOR 객체의 초기화(Initialization)를 위한 다양한 함수들을 제공한다.

* 0 벡터를 반환한다.

```C++
XMVECTOR XM_CALLCONV  noexcept XMVectorZero();
```

* (1, 1, 1, 1) 벡터를 반환한다.

```C++
XMVECTOR XM_CALLCONV  noexcept XMVectorSplatOne();
```

* $(x, y, z, w)$ 벡터를 반환한다.

```C++
XMVECTOR XM_CALLCONV  noexcept XMVectorSet(
  float x,
  float y,
  float z,
  float w
);
```

* $(s, s, s, s)$ 벡터를 반환한다.

```C++
XMVECTOR XM_CALLCONV  noexcept XMVectorReplicate(
  float Value
);
```

* $(v_x, v_x, v_x, v_x)$ 벡터를 반환한다.

```C++
XMVECTOR XM_CALLCONV  noexcept XMVectorSplatX(
  FXMVECTOR V
);
```
&nbsp;<br/>

## 연산자 오버로딩

XMVECTOR 에는 벡터 덧셈, 뺄셈, 스칼라 곱셈을 위한 Overloading 이 구현되어 있다.

[Docs: XMVECTOR Operator Overloading](https://docs.microsoft.com/en-us/windows/win32/dxmath/ovw-xmvector-operators)

* $\Vert \vec v \Vert$

```C++
XMVECTOR XM_CALLCONV  noexcept XMVector3Length(
  FXMVECTOR V
);
```

* $\Vert \vec v \Vert ^2$

```C++
XMVECTOR XM_CALLCONV  noexcept XMVector3LengthSq(
  FXMVECTOR V
);
```

* $v_1 \cdot v_2$

```C++
XMVECTOR XM_CALLCONV  noexcept XMVector3Dot(
  FXMVECTOR V1,
  FXMVECTOR V2
);
```

* $v_1 \times v_2$

```C++
XMVECTOR XM_CALLCONV  noexcept XMVector3Cross(
  FXMVECTOR V1,
  FXMVECTOR V2
);
```

* $v / \Vert v \Vert$

```C++
XMVECTOR XM_CALLCONV  noexcept XMVector3Normalize(
  FXMVECTOR V
);
```

* $ \vec V \perp x $

```C++
XMVECTOR XM_CALLCONV  noexcept XMVector3Orthogonal(
  FXMVECTOR V
);
```

* $ v_1 \measuredangle v_2$

```C++
XMVECTOR XM_CALLCONV  noexcept XMVector3AngleBetweenVectors(
  FXMVECTOR V1,
  FXMVECTOR V2
);
```

* $proj_n(v)$

```C++
void XM_CALLCONV  noexcept XMVector3ComponentsFromNormal(
  XMVECTOR  *pParallel,
  XMVECTOR  *pPerpendicular,
  FXMVECTOR V,
  FXMVECTOR Normal
);
```

* $v_1 == v_2$

```C++
bool XM_CALLCONV  noexcept XMVector3Equal(
  FXMVECTOR V1,
  FXMVECTOR V2
);
```

* $v_1 \neq v_2$

```C++
bool XM_CALLCONV  noexcept XMVector3NotEqual(
  FXMVECTOR V1,
  FXMVECTOR V2
);
```

&nbsp;<br/>

## 연산자 오버로딩 2

DirectXMath 에는 정확도는 떨어지지만 더 빠르게 추정을 할 수 있는 메소드들도 존재한다.

**속도를 위해 정확성이 조금 희생할 수 있는 상황**이라면 나쁘지 않은 선택지이다.

* $\Vert \vec V \Vert $

```C++
XMVECTOR XM_CALLCONV  noexcept XMVector3LengthEst(
  FXMVECTOR V
);
```

* $\vec V / \Vert \vec V \Vert $

```C++
XMVECTOR XM_CALLCONV  noexcept XMVector3NormalizeEst(
  FXMVECTOR V
);
```

&nbsp;<br/>

# DirectX 상수 및 함수

[Docs: Prefixed const value](https://docs.microsoft.com/ko-kr/windows/win32/dxmath/ovw-xnamath-reference-constants)

[Docs: Preimplemented functions](https://docs.microsoft.com/ko-kr/windows/win32/dxmath/ovw-xnamath-reference-functions)

&nbsp;<br/>

