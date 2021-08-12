---
layout: default
title: "DirectX Matrix"
nav_order: 11
parent: "[GameDev] DirectX"
use_math: true
---

# DirectXMath 라이브러리 의 행렬
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# DirectXMath 의 행렬

3차원 그래픽에서 점과 벡터를 변환할 때에는 $1 \times 4$ 행벡터와 $4 \times 4$ 행렬을 사용한다.<br/>
우선 $4 \times 4$ 행렬을 표현하는데 사용하는 DirectXMath 라이브러리의 형식을 살펴보자.

&nbsp;<br/>

## 형식

DirectXMath 로 $4 \times 4$ 행렬을 표현할 때 에는 [XMMATRIX](https://docs.microsoft.com/ko-kr/windows/win32/dxmath/xmmatrix-ctor) 라는 형식을 사용한다.<br/>
이 형식은 DirectXMath.h 헤더 파일에 정의되어 있다.<br/>

```cpp
struct XMMATRIX
{
    XMVECTOR r[4];
}
```

XMMATRIX 는 SIMD 를 활용하기 위해 XMVECTOR 인스턴스 4 개를 사용한다.<br/>
XMMATRIX 는 여러 생성자가 정의되어 있으며, 행렬 연산을 위해 여러 연산자들이 오버로딩 되어있다.
[XMMatrixSet](https://docs.microsoft.com/en-us/windows/win32/api/directxmath/nf-directxmath-xmmatrixset) 이라는 함수로도 XMMATRIX 인스턴스를 생성할 수 있다.

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixSet(
  float m00,
  float m01,
  float m02,
  float m03,
  float m10,
  float m11,
  float m12,
  float m13,
  float m20,
  float m21,
  float m22,
  float m23,
  float m30,
  float m31,
  float m32,
  float m33
);
```

벡터에서 자료 자료의 형식으로 XMFLOAT2(2차원), XMFLOAT3(3차원), XMFLOAT4(4차원) 을 사용하든 DirectXMath 에서 $4 \times 4$ 행렬은 [XMFLOAT4X4](https://docs.microsoft.com/en-us/windows/win32/api/directxmath/ns-directxmath-xmfloat4x4) 를 사용하는 것을 추천한다.

&nbsp;<br/>

# XMMATRIX

## 행렬 함수

* 단위행렬 $I$ 를 반환한다.

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixIdentity();
```

* $M$의 단위행렬 여부를 반환한다.

```cpp
bool XM_CALLCONV  noexcept XMMatrixIsIdentity(
  FXMMATRIX M
);
```

* 행렬 곱 AB를 반환한다.

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixMultiply(
  FXMMATRIX M1,
  CXMMATRIX M2
);
```

* 전치행렬을 반환한다.

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixTranspose(
  FXMMATRIX M
);
```

* 행렬식을 반환한다.

```cpp
XMVECTOR XM_CALLCONV  noexcept XMMatrixDeterminant(
  FXMMATRIX M
);
```

* 역행렬을 반환한다.

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixInverse(
  XMVECTOR  *pDeterminant,
  FXMMATRIX M
);
```

&nbsp;<br/>

## 매개변수

XMMATRIX 매개변수를 선언할 때에는 [XMVECTOR 매개변수](/docs/DirectX/DXVector.html#파라메타-전달)를 선언할 때와 같은 규칙을 적용한다.