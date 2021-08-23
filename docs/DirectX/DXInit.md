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

<img src="/images/DirectX/depthbuffering.png">

&nbsp;<br/>

위 그림은 물체들이 서로를 가리고 있는 간단한 장면이다. 한 물체가 다른 물체보다 앞에 있는지 판별하기 위해, D3D는 depthbuffering 또느 z-buffering 을 사용한다. 여기서 핵심은 depth buffring 을 사용하면 물체를 그리는 순서와 상관없이 서로 제대로 가려진다는 부분이다.

그림을 보면서 depthbuffering 을 이해해봅시다. 

<img src="/images/DirectX/depathbufferingillust.png">

서로 다른 물체에서 비롯된 세 객체가 View Windows 의 한 픽셀 $P$ 를 두고 경쟁을 하기 시작한다. 우선, 렌더링 작업을 수행하기 전에 back-buffer 는 기본 컬러로 cleared 된다. 그리고 depth buffer 도 cleared 되는데 보통 depth value 가 가질 수 있는 최댓값인 1로 cleared 된다. 이제 실린ㄷ, 구, 콘 순서대로 렌더링 한다고 하자. 아래 표는 픽셀 $P$ 의 요약을 나타내며 그것에 상응하는 값 $d$ 는 객체가 그려짐으로써 업데이트 된다. 다른 픽셀들도 마찬가지 과정을 거친다.

| Operation | P | d | Description |
| :----------- | :---: | :---: | :-------------|
| Clear Operation | Black | 1.0 | depth entry에 대응되는 pixel 이 초기화 된다 |
| Draw Cylinder | $P_3$ | $d_3$ | $d_3 \leq d = 1.0$ 이므로, <br/>  $P$ 버퍼를 $P_3$, $d = d_3$ 으로 업데이트한다. |
| Draw Sphere | $P_1$ | $d_1$ | $d_1 \leq d = d_3$ 이므로, <br/> $P$ 버퍼를 $P_1$, $d = d_1$ 로 업데이트 한다. |
| Draw Cone | $P_1$ | $d_1$ | $d_2 < d = d_1$ 이므로 깊이 테스트를 만족하지 못해서, <br/> 버퍼를 업데이트 하지 않는다. |

위 예에서 보듯이 깊이 값이 작은 경우에만 후면 버퍼와 깊이버퍼에 기록이 된다. 이 방식으로 관찰자와 가장 가까운 픽셀 하나만 렌더링 된다. <br/>

정리하면, depth-buffring 알고리즘은 렌더링 되는 각 픽셀의 깊이 값을 계산해서 깊이 판정을 수행함으로써 작동한다. 깊이 값은 후면 버퍼의 특정 위치에 기록될 픽셀들의 깊이를 비교해서 관찰자에게 가장 가까운 픽셀이 후면 버퍼에 기록된다.<br/>

depth-buffer 도 하나의 텍스쳐이므로 특정한 데이터 포맷을 지정할 필요가 있다. 깊이 버퍼링을 위한 데이터 포맷으로는,

1. DXGI_FORMAT_D32_FLOAT_S8X24_UINT : stencil buffer 용도로 사용되는 부호 없는 정수형 8비트와 24비트의 패딩으로만 사용되는 32bit floating-pointer
2. DXGI_FORMAT_D32_FLOAT : 각 픽셀은 32비트 float 형 깊이 값
3. DXGI_FORMAT_D24_UNORM : [0,1] 범위의 부호 없는 24비트 깊이 값과 stencil buffer 용도로 사용되는 [0,255] 사이의 8bit UINT
4. DXGI_FORMAT_D16_UNORM : [0,1] 범위의 부호 없는 16비트 깊이 값

&nbsp;<br/>

## Resources and Description

렌더링 과정에서 GPU는 back-buffer나 depth/stencil buffer 와 같은 자원에 기록하며, 표면의 모습을 표현하는 텍스처나 씬의 기하구조의 3D 위치를 담은 버퍼와 같은 리소스를 읽어들인다. 그리기 과정을 수행하기 전에, draw call 에서 참조할 리소스들을 Bind(또는 link) 해서 렌더링 파이프라인에 묶어야 한다. 몇몇 리소스들은 매번 draw call 마다 바뀌며, 그래서 우리는 필요한경우 매번 draw call 마다 바인딩을 업데이트 해주어야 한다. 하지만 GPU 리소스는 직접 바인딩되지 않습니다. 실제로 파이프라인에 바인딩 되는 것은 GPU에서 리소스를 레퍼런스 할 수 있는 가벼운 구조를 가진 descriptor 객체들입니다. 리소스 descriptor 전달은 본질적으로 간접참조입니다. GPU는 실제 데이터를 가져올 수 있고 그것의 필요한 정보를 알 수 있습니다. 우리는 draw call 에서 참조할 리소스들을 렌더링 파이프라인에 바인딩 합니다.<br/>

왜 디스크립터를 사용하여 많은 단계의 간접 참조를 사용할까요? 그 이유는, GPU 리소스는 기본적으로 메모리 덩이리이기 때문입니다. 메모리는 일반적인 형태로 유지되는 덕분에 렌더링 파이프라인의 다른 스테이지에서 사용할 수 있다. 흔한 예를 들자면, 텍스쳐를 렌더 타겟으로 사용하고(D3D가 텍스쳐에 그린다), 이후 리소스 쉐이더(텍스쳐는 표본들이 추출되고 세이더를 위한 데이터가 들어간다)로써 사용합니다. 자원 자체는 자신이 렌더 대상으로 쓰이는건지, depth/stencil 버퍼로 쓰이는 건지 shader 자원으로 쓰이는 건지 말해주지 않는다. 또한, 아마도 우리는 렌더링 파이프라인에 일부 리소스 정보만 원할 때가 있는데, 주어진 전체 리소스에서 우리는 어떻게 찾을 수 있을까요? 게다가, 리소스는 ty peless format 으로 만들어지기도 하는데, 그래서 GPU 리소스의 형태 조차도 알지 못합니다.<br/>

이런 문제를 descriptor 가 해결할 수 있다. descriptor 는 자료를 지정하는 수단 뿐 만 아니라 자원을 GPU에 알려주는 방법이기도 하다. descriptor 는 D3D 에게 자원의 사용법을 알려준다. typeless 형태인 경우에는, 그 자원을 참조하는 descriptor 를 생성할 떄 구체적인 형식을 명시할 수 있다. 

디스크립터는 자원의 사용법에 따라 여러 형식이 있다. 여기서는 아래 서술자들을 사용한다.

1. CBV/SRV/UAV 디스크립터들은 각각 상수 버퍼, 셰이더 자원, unordered access view를 정의한다.
2. Sampler descriptors 디스크립터는 텍스처에 쓰이는 sampler resources를 정의한다.
3. RTV 디스크립터는 렌더 타겟 리소스를 정의한다.
4. DSV 스크립터는 depth/stencil 리소스를 정의한다.

descriptor heap 는 디스크립터 배열이다. 응용 프로그램에서 사용하는 디스크립터들이 저장되는 곳이 바로 descriptor heap 이다. 디스크립터 종류마다 개별적인 디스크립터 힙이 필요하다. 같은 종류의 디스크립터들은 같은 디스크립터 힙에 저장된다. 또한, 한 종류의 디스크립터에 대해 여러 개의 힙을 돌 수 있다.


같은 리소스 안에 다양한 디스크립터 레퍼런싱을 가지게 할 수 있다. 예를 들어, 하나의 리소스 안에 다른 영역들을 가르키는 다수의 디스크립터를 가질 수 있다. 또한, 앞서 말했듯이, 리소스는 렌더링 파이프라인의 다른 스테이지 안에 바운드 될 수 있다. 각각의 스테이지마다, 다른 디스크립터가 필요하다. 텍스쳐를 렌더타겟이나 쉐이더 리소스를 사용하는 것으로 예를 들면, 우리는 두개의 디스크립터를 만들어야 합니다. RTV 타입의 디스크립터와, SRV 타입의 디스크립터를. 유사하게, 만약 typeless 포맷의 리소스를 만들엇다면, 텍스쳐의 엘리먼트를 부동소수점 값이나 정수형으로 사용하는 것이 가능한데, 이것은 두개의 디스크립터, 하나는 부동소수점 디스크립터를, 다른 하나는 정수 형태를 요구한다.

디스크립터는 초기화 시점에 생성해야한다. 왜냐하면 유형 검사와 유효성 검사가 발생하기 때문이고, 런타임보다 초기화 시점에 생서하는 것이 더 낫기 때문이다.

&nbsp;<br/>

&nbsp;<br/>

&nbsp;<br/>

&nbsp;<br/>
