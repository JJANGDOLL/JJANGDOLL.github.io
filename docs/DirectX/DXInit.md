---
layout: default
title: "DirectX Init"
nav_order: 11
parent: "[GameDev] DirectX"
use_math: true
---

# DirectX Initialize
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 사전지식

D3D 초기화 공정 이해를 위해선 몇 가지 기본적인 그래픽 개념과 D3D 형식들을 숙지할 필요가 있다.

&nbsp;<br/>

## Direct3D 12의 개요

Direct3D 는 응용 프로그램에서 GPU(graphic processing unit) 를 제어하고 프로그래밍 하기 위해 쓰이는 저수준 그래픽 API(applicaiton programming interface) 이다. 이것을 통해서 응용 프로그램은 3차원 그래픽 가속 기능을 이용해 3차원 세계를 렌더링 할 수 있게 된다. 예를 들어 화면을 깨끗이 지우고 싶고 이 명령을 GPU에 전달하고 싶다면 Direct3D 의 ID3D12CommandList::ClearRenderTargetView 라는 메서드를 호출하면 된다. 응용 프로그램과 그래픽 하드웨어 사이에 Direct3D 라는 간접 계층과 하드웨어 드라이버가 Direct3D 명령들을 GPU가 이해할 수 있는 고유한 기계어로 번역해 주므로, 해당 GPU가 Direct3D 12 를 지원하는 한 개발자는 GPU의 세부사항을 걱정할 필요가 없다. (단, NVIDIA, Intel, AMD 같은 GPU 제조사들이 Microsoft 의 Direct3D 팀과 협력해서 Direct3D 기준을 준수하는 드라이버를 제공해야 한다.)<br/>

D3D12 에는 몇 가지 새로운 렌더링 기능이 추가되었으며, 주요 개선점은 이전 버전들에 비해 CPU의 부담을 줄이고 다중 스레드 지원을 개선하기 위해 설계를 다시 하였다. 이 성능상의 목표 달성을 위해 D3D12는 D3D11보다 훨씬 낮은 저수준의 API가 되었다. D3D12는 추상화가 줄었고, 개발자가 손수 관리해야 할 사항이 늘었으며 GPU 아키텍처를 좀 더 밀접하게 반영한다. API가 사용하기가 어려워졌지만 성능이 개선되었다.

&nbsp;<br/>

## COM(Component Object Model)

DirectX 측면에서 COM은 DX의 프로그래밍 언어 독립성과 하위 호환성을 가능하게 하는 기술이다. COM 인터페이스를 C++ 의 new 로 생성할 필요도 없다. delete 마찬가지로 사용할 필요가 없다. COM 인터페이스의 메소드를 사용해 얻고, Release 메소드를 호출해주어야 한다. <br/>

COM 객체의 수명 관리를 위해 WRL(Windows Runtime Library)는 Microsoft::WRL::ComPtr 이라는 클래스를 제공한다. ComPtr 인스턴스는 범위를 벗어난 하위 COM 객체에 대해 자동으로 Release 를 호출해준다. 따라서 프로그래머가 직접 Release 를 호출할 필요가 없다. 여기서 사용하는 ComPtr 의 메소드는 다음 세가지 이다.

1. Get : 이 ComPtr 과 관련된 인터페이스 주소를 반환합니다.

```cpp
ComPtr<ID3D12RootSignature> mRootSignature;

mCommandList->SetGraphicsRootSignature(mRootSignature.Get());
```

2. GetAddressOf : 이 ComPtr이 가르키는 보호되는 데이터 멤버에 대한 포인터의 주소를 반환합니다.

```cpp
ComPtr<ID3D12CommandAllocator> mDirectCmdListAlloc;

ThrowIfFailed(md3dDevice->CreateCommandAllocator(
    D3D12_COMMAND_LIST_TYPE_DIRECT,
    mDirectCmdListAlloc.GetAddressOf()));
```

3. Reset : ComPtr 인스턴스를 nullptr로 설정하고 하위 COM 인터페이스의 참조 횟수를 1 감소시킨다.

&nbsp;<br/>

## 텍스처 형식

2차원 텍스처는 자료 원소들의 2차원 배열이다. 2차원 텍스처의 첫 번째 용도는 2차원 이미지 자료를 젖아하는 것인데, 이때 텍스처의 각 원소는 픽셀 하나의 색상을 포함한다. 하지만, 이 뿐만 아니라 normal mapping 이라고 하는 고급 기술은 각각의 원소들이 컬러 대신 3D vector 정보들이 저장되어 있다. 이 예제에서 알 수 있드시 실제로는 훨씬 범용적이다. 마찬가지로, 1차원 텍스처는 1차원 배열이고, 3차원 텍스처는 3차원 배열이다. 다만, 텍스처가 단순한 자료 배열은 아니다. 그것들은 mipmal 레벨을 가질 수 있고, GPU 그것들을 위한 필터 적용과 멀티 샘플링 같은 특별한 연산자를 처리할 수 있다. 하지만 텍스처는 아무 자료나 담을 수 없다는 것도 중요하다. 텍스처에는 정해진 형식의 데이터원소 형식만 저장할 수 있는데, 이 데이터들은 DXGI_FORMAT 이라는 열거형으로 정의되어 있다.

1. DXGI_FORMAT_R32G32B32_FLOAT : 각 원소는 32비트 부동 소수점 성분 세 개로 이루어진다.
2. DXGI_FORMAT_R16G16B16A16_UNORM : 각 원소는 [0, 1]  범위로 매핑되는 16비트 성분 네 개로 이루어진다.
3. DXGI_FORMAT_R32G32_UINT : 각 원소는 부호 없는 32비트 정수 성분 두 개로 이루어진다.
4. DXGI_FORMAT_R8G8B8A8_UNORM : 각 원소는 [0, 1] 범위로 매핑되는 부호 없는 8비트 성분 네 개로 이루어진다.
5. DXGI_FORMAT_R8G8B8A8_SNORM : 각 원소는 [-1, 1] 범위로 매핑되는 부호 있는 8비트 성분 네 개로 이루어진다.
6. DXGI_FORMAT_R8G8B8A8_SINT : 각 원소는 [-128, 127] 범위로 매핑되는 부호 있는 8비트 정수 성분 네 개로 이루어진다.
7. DXGI_FORMAT_R8G8B8A8_UINT : 각 원소는 [0, 266] 구간으로 사상되는 부호 없는 8비트 정수 성분 네 개로 이루어진다.

여기서 R, G, B, A 는 Red, Green, Blue, Alpha 를 뜻한다. 그래픽에서 하나의 색상은 3원색인 적, 녹, 청 색의 조합으로 이루어진다. 알파 채널은 투명도를 제어하는 데 쓰인다. 다만, 텍스처에 반드시 색상을 담을 필요는 없다. 아래같은 텍스처 원소 형식은 부동수소점 성분 세 개로 구성되며, 좌표성분들이 float 타입인 임의의 3차원 벡터를 담을 수 있다.

DXGI_FORMAT_R32G32B32_FLOAT

메모리만 예약한 후 텍스처가 파이프라인에 바인딩 될 때 ㅏㄴ중에 데이터를 재해석 하는 방법을 지정하는 typeless 형식들도 있습니다.
예를 들어, 아래 typeless 형태의 엘리멘트들은 16bit comeponents 를 예약하지만, 형식이 지정되어 있지는 않았습니다.

DXGI_FORMAT_R1G16B16A16_TYPELESS

&nbsp;<br/>

## 스왑체인과 페이지 전환

애니메이션이 깜박이는 현상을 피하기 위한 좋은 방법은, 백버퍼 라고 불리우는 off-screen texture 에 애니메이션의 모든 프레임을 그리는 것이다. 프레임의 모든 애니메이션 장면이 백버퍼에 그려지면, 하나의 완성된 프레임을 화면에 표시해줍니다. 이 방법으로, 관찰자는 프레임이 그려지는 장면을 볼 수 없고 오직 완성된 프레임만 볼 수 있다. 이것을 구현하기 위해선, 하드웨어에 두개의 텍스쳐 버퍼를 유지해야 하는데, 하나는 front-buffer 라고 불리우고 다른 하나는 back-buffer 라고 불리운다. front-buffer 는 현재 모니터에 보여지는 이미지 데이터가 저장되어 있으며, 다음 애니메이션의 프레임은 back-buffer 에 그려지고 있다. back-buffer 에 프레임이 모두 그려진 후에는 front-buffer 와 back-buffer의 역할이 바뀌게 된다. back-buffer 는 front-buffer 가 되고 front-buffer 는 back-buffer가 되어 다음 프레임의 애니메이션을 준비한다. back & front buffer 의 역할 변경을 presenting 이라고 부른다. 현재 front-buffer 와 back-buffer의 단순한 포인터 변경이기 때문에 Preseinting 은 효율적인 연산이다.<br/>

&nbsp;<br/>

<img src="/images/DirectX/doublebuffering.png">

&nbsp;<br/>

front & back buffer 의 형태를 swap chain 이라고 부른다. D3D에서, swapchain 은 IDXGISwapChain 인터페이스로 대표된다. 이 인터페이스는 front & back 버퍼를 저장하고, resizing 과 presenting 함수를 제공한다.<br/>

위처럼 두개의 버퍼를 사용하는 방법을 double buffering 이라고 부른다. 더해서 두 개 이상인, 3개의 버퍼를 쓰는 것을 triple buffering 이라고 부른다. 그렇지만 2개면 대게로 충분하다.

&nbsp;<br/>

## Depth Buffering

깊이 버퍼는 텍스처에 이미지 자료가 있지 않는 예시이다. 이것은 각 픽셀의 깊이 정보를 가지고 있다. 이 값은 0~1 범위만 가능하다. 0 은 관잘차의 절두채 안에서 가장 가까운 물체에 해당하고, 1은 가장 먼 물체에 해당한다.  depth buffer와 back-buffer 사이에는 일대일 대응이 가능하다. 그러므로 백퍼버가 1280 $\times$ 1024 의 해상도를 가지고 있다면, depth-buffer 또한 1280  $\times$ 1024 크기를 가지고 있다.<br/>



&nbsp;<br/>

&nbsp;<br/>

&nbsp;<br/>
