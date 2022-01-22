---
layout: default
title: "DirectX Transformation"
nav_order: 20
parent: "[GameDev] DirectX"
use_math: true
---

# DirectXMath 라이브러리 의 Transformation
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# DirectXMath 의 Transformation

## DirectXMath 의 변환 함수 

* 비례행렬 생성

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixScaling(
  float ScaleX,
  float ScaleY,
  float ScaleZ
);
```

* 벡터의 성분으로 비례행렬 생성

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixScalingFromVector(
  FXMVECTOR Scale
);
```

* $x$ 축에 대한 회전행렬 $R_x$ 생성

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixRotationX(
  float Angle
);
```

* $y$ 축에 대한 회전행렬 $R_y$ 생성

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixRotationY(
  float Angle
);
```

* $z$ 축에 대한 회전행렬 $R_z$ 생성

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixRotationZ(
  float Angle
);
```

* 임의의 축 $n$ 에 대한 회전 행렬 $R_n$ 생성

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixRotationAxis(
  FXMVECTOR Axis,
  float     Angle
);
```

* 이동행렬 생성

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixTranslation(
  float OffsetX,
  float OffsetY,
  float OffsetZ
);
```

* 벡터의 성분들로 이동행렬 생성

```cpp
XMMATRIX XM_CALLCONV  noexcept XMMatrixTranslationFromVector(
  FXMVECTOR Offset
);
```

* 점과 행렬의 곱. $\vec v M$. 점 변환을 위해 $v_w = 1$ 으로 둔다.

```cpp
XMVECTOR XM_CALLCONV  noexcept XMVector3TransformCoord(
  FXMVECTOR V,
  FXMMATRIX M
);
```

* 벡터와 행렬의 곱. $\vec vM$. 벡터 변환을 위해 $v_w = 0$ 으로 둔다.

```cpp
XMVECTOR XM_CALLCONV  noexcept XMVector3TransformNormal(
  FXMVECTOR V,
  FXMMATRIX M
);
```