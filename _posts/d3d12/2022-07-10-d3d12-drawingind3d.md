---
title: "D3D 그리기 연산"

categories:
  - D3D12


tags:
  - d3d12
  - drawing in d3d

toc: true
toc_label: D3D 그리기 연산
toc_sticky: true

sidebar_main: true
---

렌더링 파이프 라인을 구성하고, VS, PS 를 정의하고 기하구조를 렌더링 파이프라인에 제출해서 3D 물체를 그리는 데 필요한 D3D API 의 여러 인터페이스와 메소드를 살펴본다.

<br/>

# Vertices and Input Layouts

D3D 정점에는 공간 위치 이외의 추가적인 자료를 부여할 수 있다.

```cpp
struct Vertex1
{
 XMFLOAT3 Pos;
 XMFLOAT4 Color;
};
struct Vertex2
{
 XMFLOAT3 Pos;
 XMFLOAT3 Normal;
 XMFLOAT2 Tex0;
 XMFLOAT2 Tex1;
};
```

이렇게 만든 정점 구조체에 대해서 D3D에게 각 성분으로 뭘 해야한느지 알려줘야 한다. D3D에게 알려주는 수단으로 쓰이는 것이 input layout description(입력 배치 서술) 이다. 이 desciptor는 D3D12_INPUT_LAYOUT_DESC 라는 구조체로 대표된다.

```cpp
typedef struct D3D12_INPUT_LAYOUT_DESC
{
 const D3D12_INPUT_ELEMENT_DESC *pInputElementDescs;
 UINT NumElements;
} D3D12_INPUT_LAYOUT_DESC;
```

이 desciptor 는 D3D12_INPUT_ELEMENT_DESC 배열과 그 원소의 갯수를 가지고 있다.

D3D12_INPUT_ELEMENT_DESC 는 정점 구조체의 각 성분을 서술한다. 이 배열의 원소들과 정점 구조체의 성분들은 일대일로 대응되야 한다. 즉 정점 구조체 성분이 두개면, D3D12_INPUT_ELEMENT_DESC 배열에는 원소가 두개있어야 한다.

```cpp
typedef struct D3D12_INPUT_ELEMENT_DESC
{
 LPCSTR SemanticName;
 //성분에 부여된 문자열 이름. VS에서 semantic 이름으로 쓰이니 반드시 유효해야함. semantic 은 정점 구조체 성분을 VS 입력 서명과 대응시키는 역할을 한다.
 UINT SemanticIndex;
 // semantic 에 부여된 색인
 DXGI_FORMAT Format;
 // DXGI_FORMAT 열거형의 한 멤버. 정점 성분의 자료형식을 D3D에게 알려주는 역할
 UINT InputSlot;
 // 성분의 자료를 가져올 정점 버퍼 슬롯의 색인. D3D에는 총 16개의 정점 버퍼 슬롯을 통해서 정점 자료를 공급할 수 있다.
 UINT AlignedByteOffset;
 // 정점 구조체으 ㅣ시작 위치와 이 정점 성분의 시작 위치 사이의 거리를 나타내는 오프셋
 D3D12_INPUT_CLASSIFICATION InputSlotClass;
 // 일단은 D3D12_INPUT_PER_VERTEX_DATA 를 지정. 다른 값은 고급 기법에 사용된다.
 UINT InstanceDataStepRate;
 // 일단 0. 다른 값은 고급 기법에 사용
} D3D12_INPUT_ELEMENT_DESC;
```

> semantic name 추가설명

![6_1_1](/assets/images/d3d12/6_1_1.png)

Vertex1 과 Vertex2 는 아래 input layout descriptor 를 쓰면 된다.

```cpp
D3D12_INPUT_ELEMENT_DESC desc1[] =
{
 {"POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,
 D3D12_INPUT_PER_VERTEX_DATA, 0},
 {"COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12,
 D3D12_INPUT_PER_VERTEX_DATA, 0}
};
D3D12_INPUT_ELEMENT_DESC desc2[] =
{
 {"POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,
 D3D12_INPUT_PER_VERTEX_DATA, 0},
 {"NORMAL", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 12,
 D3D12_INPUT_PER_VERTEX_DATA, 0},
 {"TEXCOORD", 0, DXGI_FORMAT_R32G32_FLOAT, 0, 24,
 D3D12_INPUT_PER_VERTEX_DATA, 0}
 {"TEXCOORD", 1, DXGI_FORMAT_R32G32_FLOAT, 0, 32,
 D3D12_INPUT_PER_VERTEX_DATA, 0}
};
```

<br/>

# Vertex buffer

GPU가 정점 배열에 접근하려면 그 정점들을 buffer라고 부르는 ID3D12Resource 에 넣어둬야 한다. 정점들을 저장하는 버퍼를 vertex buffer 라고 부른다. 버퍼는 텍스쳐보단 단순한 자원이다.

정점 버퍼를 생성할며ㅕㄴ D3D12_RESOURCE_DESC 를 채우고 ID3D12Device::CreateCommittedResource 메소드를 호출해 ID3D12Resource 객체를 생성한다. D3D12는 편의용 생성자와 메소드를 추가한 C++ 레퍼클래스 CD3DX12_RESOURCE_DESC 를 제공한다.

```cpp
static inline CD3DX12_RESOURCE_DESC Buffer(
 UINT64 width,
 D3D12_RESOURCE_FLAGS flags = D3D12_RESOURCE_FLAG_NONE,
 UINT64 alignment = 0 )
{
 return CD3DX12_RESOURCE_DESC( D3D12_RESOURCE_DIMENSION_BUFFER,
 alignment, width, 1, 1, 1,
 DXGI_FORMAT_UNKNOWN, 1, 0,
 D3D12_TEXTURE_LAYOUT_ROW_MAJOR, flags );
}
```

범용 GPU 자원으로서의 너비(width)는 가로 길이가 아니라 버퍼의 바이트 개수를 뜻한다. 예를 들어 float 64 개를 담는 버퍼의 너비는 64 * sizeof(float) 이다.

> CD3DX12_RESOURCE_DESC 클래스는 텍스처 자원을 서술하는 D3D12_RESROUCE_DESC 의 생성을 돕는 편의용 메소드들과 자원에 관한 정보를 조회하는 편의 메소드들도 제공한다.
> 1. CD3DX12_RESOURCE_DESC::Tex1D
> 2. CD3DX12_RESOURCE_DESC::Tex2D
> 3. CD3DX12_RESOURCE_DESC::Tex3D

> 2차원 텍스쳐인 깊이 스탠실 버퍼 역시 ID3D12Resource 객체로 대표된다. ID3D12Resource 인터페이스는 D3D12의 모든 자원을 대표한다. 반면 D3D11 에서는 자원마다 인터페이스가 달랐다.

정적 기하구조(프레임마다 변하지 않는 기하구조)를 그릴 떈 최적의 성능을 위해 D3D12_HEAP_TYPE_DEFAULT 에 넣는다. 일반적으로 게임의 대부분 기하구조는 정적이다.(배경, 지형, 무기). 정적 기하구조의 경우, 정점 버퍼들을 초기화한 후에는 GPU만 버퍼의 정점을 읽으니 기본 힙에 넣는 것이 합당하다. CPU는 기본 힙에 있는 정점 버퍼를 수정할 수 없다.

응요 프로그램이 정점 버퍼를 초기ㅗ하하는지 알아보자. 실제 정점 버퍼 자원을 생성하는 것과 더불어, APP은 D3D12_HEAP_TYPE_UPLOAD 형식의 힙에 임시 업로드용 자료를 복사하려면 업로드 힙에 자원을 맡겨야 한다. 업로드 버퍼를 생성한 후엔 시스템 메모리에 있는 정점 자료를 업로드 버퍼에 복사하고, 그런 업로드 버퍼의 정점 자료를 실제 정점 버퍼로 복사한다.

기본 버퍼(D3D12_HEAP_TYPE_DEFAULT 형식의 힙에 있는 버퍼)의 자료를 초기화하려면 항상 입시 업로드 버퍼가 필요하니 여기선 아래 편의용 함수를 제공한다.

```cpp
Microsoft::WRL::ComPtr<ID3D12Resource> d3dUtil::CreateDefaultBuffer(
    ID3D12Device* device,
    ID3D12GraphicsCommandList* cmdList,
    const void* initData,
    UINT64 byteSize,
    Microsoft::WRL::ComPtr<ID3D12Resource>& uploadBuffer)
{
    ComPtr<ID3D12Resource> defaultBuffer;

    // Create the actual default buffer resource.
    ThrowIfFailed(device->CreateCommittedResource(
        &CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_DEFAULT),
        D3D12_HEAP_FLAG_NONE,
        &CD3DX12_RESOURCE_DESC::Buffer(byteSize),
		D3D12_RESOURCE_STATE_COMMON,
        nullptr,
        IID_PPV_ARGS(defaultBuffer.GetAddressOf())));

    // In order to copy CPU memory data into our default buffer, we need to create
    // an intermediate upload heap. 
    ThrowIfFailed(device->CreateCommittedResource(
        &CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_UPLOAD),
		D3D12_HEAP_FLAG_NONE,
        &CD3DX12_RESOURCE_DESC::Buffer(byteSize),
		D3D12_RESOURCE_STATE_GENERIC_READ,
        nullptr,
        IID_PPV_ARGS(uploadBuffer.GetAddressOf())));


    // Describe the data we want to copy into the default buffer.
    D3D12_SUBRESOURCE_DATA subResourceData = {};
    subResourceData.pData = initData;
    subResourceData.RowPitch = byteSize;
    subResourceData.SlicePitch = subResourceData.RowPitch;

    // Schedule to copy the data to the default buffer resource.  At a high level, the helper function UpdateSubresources
    // will copy the CPU memory into the intermediate upload heap.  Then, using ID3D12CommandList::CopySubresourceRegion,
    // the intermediate upload heap data will be copied to mBuffer.
	cmdList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(defaultBuffer.Get(), 
		D3D12_RESOURCE_STATE_COMMON, D3D12_RESOURCE_STATE_COPY_DEST));
    UpdateSubresources<1>(cmdList, defaultBuffer.Get(), uploadBuffer.Get(), 0, 0, 1, &subResourceData);
	cmdList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(defaultBuffer.Get(),
		D3D12_RESOURCE_STATE_COPY_DEST, D3D12_RESOURCE_STATE_GENERIC_READ));

    // Note: uploadBuffer has to be kept alive after the above function calls because
    // the command list has not been executed yet that performs the actual copy.
    // The caller can Release the uploadBuffer after it knows the copy has been executed.


    return defaultBuffer;
}
```

D3D12_SUBRESOURCE_DATA 구조체는 아래와 같다.

```cpp
typedef struct D3D12_SUBRESOURCE_DATA
{
 const void *pData;
 // 버퍼 초기화용 자료를 담은 시스템 메모리 배열을 가르키는 포인터.
 LONG_PTR RowPitch;
 // 복사할 자료의 크기(바이트 갯수)
 LONG_PTR SlicePitch;
 // 복사할 자료의 크기(바이트 갯수)
} D3D12_SUBRESOURCE_DATA;
```

정점 8개, 각자 다른 색상의 기본 버퍼를 이 클래스를 이용해서 생성하는 방법이다.

```cpp
struct Vertex
{
 XMFLOAT3 Pos;
 XMFLOAT4 Color;
};

Vertex vertices[] =
{
 { XMFLOAT3(-1.0f, -1.0f, -1.0f), XMFLOAT4(Colors::White) },
 { XMFLOAT3(-1.0f, +1.0f, -1.0f), XMFLOAT4(Colors::Black) },
 { XMFLOAT3(+1.0f, +1.0f, -1.0f), XMFLOAT4(Colors::Red) },
 { XMFLOAT3(+1.0f, -1.0f, -1.0f), XMFLOAT4(Colors::Green) },
 { XMFLOAT3(-1.0f, -1.0f, +1.0f), XMFLOAT4(Colors::Blue) },
 { XMFLOAT3(-1.0f, +1.0f, +1.0f), XMFLOAT4(Colors::Yellow) },
 { XMFLOAT3(+1.0f, +1.0f, +1.0f), XMFLOAT4(Colors::Cyan) },
 { XMFLOAT3(+1.0f, -1.0f, +1.0f), XMFLOAT4(Colors::Magenta) }
};

const UINT64 vbByteSize = 8 * sizeof(Vertex);
ComPtr<ID3D12Resource> VertexBufferGPU = nullptr;
ComPtr<ID3D12Resource> VertexBufferUploader = nullptr;
VertexBufferGPU = d3dUtil::CreateDefaultBuffer(md3dDevice.Get(),
 mCommandList.Get(), vertices, vbByteSize, VertexBufferUploader);
```

정점 버퍼를 파이프 라인에 묶으려면 정점 버퍼 자원을 서술하는 정점 버퍼 뷰를 만들어야 한다. 정점 버퍼 뷰를 대표하는 형식은 D3D12_VERTEX_BUFFER_VIEW

```cpp
typedef struct D3D12_VERTEX_BUFFER_VIEW
{
 D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;
 // 생성할 뷰의 대상이 되는정점 버퍼 자원의 가상 주소 이 주소는 ID3D12Resource::GetGPUVirtualAddress 메소드로 얻을 수 있다.
 UINT SizeInBytes;
 // BufferLocation 에서 시작하는 정점 버퍼의 크기
 UINT StrideInBytes;
 // 버퍼에 담긴 한 정점 원소의 크기
} D3D12_VERTEX_BUFFER_VIEW;
```

정점 버퍼를 생성하고 그에 대한 뷰를 생성했다면, 정점 버퍼를 파이프라인의 입력 슬롯에 묶을 수 있다. 그러면 정점들이 파이프라인의 IA 로 공급된다.

```cpp
void ID3D12GraphicsCommandList::IASetVertexBuffers(
 UINT StartSlot,
 // 시작 슬롯. 즉 첫째 정점 버퍼를 묶을 입력 슬롯의 색인
 UINT NumBuffers,
 // 입력 슬롯들에 묶을 정점 버퍼 갯수.
 const D3D12_VERTEX_BUFFER_VIEW *pViews
 // 정점 버퍼 뷰 배열의 첫 원소를 가르키는 포인터
 );
```

호출 예시

```cpp
D3D12_VERTEX_BUFFER_VIEW vbv;
vbv.BufferLocation = VertexBufferGPU->GetGPUVirtualAddress();
vbv.StrideInBytes = sizeof(Vertex);
vbv.SizeInBytes = 8 * sizeof(Vertex);
D3D12_VERTEX_BUFFER_VIEW vertexBuffers[1] = { vbv };
mCommandList->IASetVertexBuffers(0, 1, vertexBuffers);
```

입력 슬롯에 묶은 정점 버퍼는 다시 변경하지 않는 한 계속 그 입력 슬롯에 묶여있다. 따라서, 정점 버퍼를 여러 개 사용하는 경우 코드의 전반적인 구조를 다음과 같아진다.

```cpp
ID3D12Resource* mVB1; // stores vertices of type Vertex1
ID3D12Resource* mVB2; // stores vertices of type Vertex2
D3D12_VERTEX_BUFFER_VIEW_DESC mVBView1; // view to mVB1
D3D12_VERTEX_BUFFER_VIEW_DESC mVBView2; // view to mVB2
/*...Create the vertex buffers and views...*/
mCommandList->IASetVertexBuffers(0, 1, &VBView1);
/* ...draw objects using vertex buffer 1... */
mCommandList->IASetVertexBuffers(0, 1, &mVBView2);
/* ...draw objects using vertex buffer 2... */
```

정점 버퍼를 입력 슬롯에 설정한다고 버퍼의 정점들이 그려지진 않는다. 단지 파이프라인에 공급할 준비가 됬을 뿐이다. 실제로 그리려면 GraphicsCommandList::DrawInstanced 메소드를 호출해야 한다.

```cpp
void ID3D12CommandList::DrawInstanced(
 UINT VertexCountPerInstance,
 // 인스턴스당 그릴 정점들의 갯수
 UINT InstanceCount,
 // 그릴 인스턴스 갯수. 인스턴싱이라 부르는 고급 기법에서는 여러개의 인스턴스를 그리지만, 보통의 경우에는 인스턴스 하나만 그리므로 1로 설정
 UINT StartVertexLocation,
 // 정점 버퍼에서 이 그리기 호출로 그릴 일련의 정점들 중 첫 정점 색인(0 시작)
 UINT StartInstanceLocation);
 // 고급 기법인 인스턴싱에 쓰임
```

두 매개변수 VertexCountPerInstance, StartVertexLocation 에 의해 정점 버퍼의 정점들 중 이 그리기 호출에 쓰이는 일련의 정점들이 결정된다.

![6_2_1](/assets/images/d3d12/6_2_1.png)

DrawInstanced 메소드를 보면 주어진 정점들로 그려질 기본 도형이 어떤 종류인지에 관한 매개변수가 없다. 선 목록인지, 삼각형 목록인지 등으로 취급할 지는 ID3D12GraphicsCommandList::IASetPrimitiveTopology 메소드로 설정하는 기본도형 위상구조 상태가 결정한다.

```cpp
cmdList->IASetPrimitiveTopology(D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
```

<br/>

# Indeces and Index Buffer

정점들과 마찬가지로 GPU가 색인들의 배열에 접근할 수 있으려면 색인도 GPU 자원(ID3D12Resource)에 넣어둬야 한다. index 가 담긴 버퍼를 indexbuffer 라고 부른다. 예제 프레임워크의 d3dUtil::CreateDefaultBuffer 함수는 void* 를 통해서 일반적 자료를 처리하니 색인버퍼나 그 외의 모든 기본 버퍼도 이 함수로 생성 가능하다.

index buffer 를 파이프라인에 묶으려면 인덱스 버퍼 자원을 서술하는 인덱스 버퍼 뷰를 만들어야 한다. 정점 버퍼 뷰 처럼 인덱스 버퍼 뷰 에도 서술자 힙이 필요하지 않다. 인덱스 버퍼 뷰를 대표하는 형식은 D3D12_INDEX_BUFFER_VIEW 이다.

```cpp
typedef struct D3D12_INDEX_BUFFER_VIEW
{
 D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;
 // 생성할 뷰의 대상이 되는 버텍스 버퍼 자원의 가상주소. 
 UINT SizeInBytes;
 // BufferLocation 에서 시작하는 색인 버퍼의 크기
 DXGI_FORMAT Format;
 // 색인의 형식
} D3D12_INDEX_BUFFER_VIEW;
```

색인버퍼를 사용하려면 파이프라인에 묶어야 한다. 색인 버퍼는 ID3D12CommandList::SetIndexBuffer 메소드를 통해서 IA에 묶는다.

```cpp
std::uint16_t indices[] = {
 // front face
 0, 1, 2,
 0, 2, 3,
 // back face
 4, 6, 5,
 4, 7, 6,
 // left face
 4, 5, 1,
 4, 1, 0,
 // right face
 3, 2, 6,
 3, 6, 7,
 // top face
 1, 5, 6,
 1, 6, 2,
 // bottom face
 4, 0, 3,
 4, 3, 7
}; 

const UINT ibByteSize = 36 * sizeof(std::uint16_t);
ComPtr<ID3D12Resource> IndexBufferGPU = nullptr;
ComPtr<ID3D12Resource> IndexBufferUploader = nullptr;
IndexBufferGPU = d3dUtil::CreateDefaultBuffer(md3dDevice.Get(),
 mCommandList.Get(), indices), ibByteSize, IndexBufferUploader);
D3D12_INDEX_BUFFER_VIEW ibv;
ibv.BufferLocation = IndexBufferGPU->GetGPUVirtualAddress();
ibv.Format = DXGI_FORMAT_R16_UINT;
ibv.SizeInBytes = ibByteSize;
mCommandList->IASetIndexBuffer(&ibv);
```

인덱스를 이용해서 기본 도형을 그리려면 ID3D12GraphicsCommandList::DrawIndexedInstanced 메소드를 사용한다.

```cpp
void ID3D12GraphicsCommandList::DrawIndexedInstanced(
 UINT IndexCountPerInstance,
 // 그리기에 사용할 인덱스 갯수(인스턴스당)
 UINT InstanceCount,
 // 그릴 인스턴스 갯수. 인스턴싱이라 부르는 고급 기법에는 여러개 인스턴스를 그린다.
 UINT StartIndexLocation,
 // 인덱스 버퍼의 인덱스 중 이 그리기 호출에 사용할 첫 색인
 INT BaseVertexLocation,
 // 이 그리기 호출에 쓰이는 색인에 더할 정수 값. 더한 결과를 최종 색인으로 사용해 정점 버퍼에서 정점을 가져온다.
 UINT StartInstanceLocation);
 // 인스턴싱에 쓰인다.
```

구, 육면체, 원기둥으로 이루어진 장면을 그린다고 하자. 처음에는 세 물체에 개별적인 정점 버퍼와, 인덱스 버퍼가 있다. 각 지역 인덱스버퍼의 색인들은 그에 해당하는 지역 정점 버퍼를 기준으로 한다. 그러나, 실제로 그릴 때는 하나의 정점버퍼와 인덱스 버퍼로 합친다고 하자. 버퍼들을 합치면 색인이 잘못된 정점을 가르키게 된다. 이유는 각 색인은 개별 정점 버퍼를 기준으로 했지 합쳐진 정점 버퍼를 기준으로 하지 않았기 때문이다.

![6_3_1](/assets/images/d3d12/6_3_1.png)

버퍼들을 병합하고 나면 육면체 정점 버퍼의 실제 색인은 아래와 같다.

```text
firstBoxVertexPos,
firstBoxVertexPos+1,
...
firstBoxVertexPos+numBoxVertices-1
```

결론적으로 인덱스 버퍼의 색인을 갱신하려면 모든 육면체 색인에 firstBoxVertexPos를 더해야 한다. 전역 정점 버퍼에서 한 물체의 첫 번쨰 정점의 위치를 그 물체의 기준 정점 위치(base vertex location)이라고 부르기로 하자. 일반적으로 새 색인은 각 색인에 해당 기준 정점 위치를 더한 값이다. 이러한 색인 갱신 작업을 CPU에서 직접 수행할 필요 없이 DrawIndexedInstanced 넷째 매개변수로 기준 정점 위치를 지정하면 D3D 가 알아서 처리한다.

```cpp
mCmdList->DrawIndexedInstanced(
 numSphereIndices, 1, 0, 0, 0);
mCmdList->DrawIndexedInstanced(
 numBoxIndices, 1, firstBoxIndex, firstBoxVertexPos, 0);
mCmdList->DrawIndexedInstanced(
 numCylIndices, 1, firstCylIndex, firstCylVertexPos, 0);
```

<br/>

# Example Vertex Shader

간단한 정점 셰이더 구현이다.

```hlsl
cbuffer cbPerObject : register(b0)
{
 float4x4 gWorldViewProj;
};

void VS(float3 iPosL : POSITION,
 float4 iColor : COLOR,
 out float4 oPosH : SV_POSITION,
 out float4 oColor : COLOR)
{
 // Transform to homogeneous clip space.
 oPosH = mul(float4(iPosL, 1.0f), gWorldViewProj);
 // Just pass vertex color into the pixel shader.
 oColor = iColor;
}
```

셰이더는 HLSL(high level shading language)이라고 하는 언어로 작성한다.

본질적으로 정점 셰이더는 하나의 함수이다. 여기선 VS라는 이름을 사용하지만, 유효한 함수 이름이면 어떤것이든 사용 가능하다. 지금 예제는 매개변수가 네 개인데, 청므 둘은 입력 매개변수이고 나머지 둘은 출력 매개변수이다. HLSL는 레퍼런스나 포인터가 없으므로 함수가 여러 개의 값을 돌려주려면 구조체를 쓰거나 out이 지정된 출력 매개변수를 사용해야 한다. HLSL에서 함수는 항상 인라인 된다.

![6_4_1](/assets/images/d3d12/6_4_1.png)

처음 두 입력 매개변수는 정점 셰이더의 input signature 을 형성한다. 이들은 현재 그리기 작업에 쓰이는 커스텀 정점 구조체의 멤버들에 대응된다. 매개변수 siementac (".:POSITION", ":COLOR")는 정점 구조체의 멤버들을 정점 셰이더 입력 매개변수들에 대응시키는 역할을 한다. 

출력 매개변수에도 siementac이 부여되어 있다. (":SV_POSITION", ":COLOR") 이들은 정점 셰이더의 출력을 파이프라인의 다음 단계의 해당 입력에 대응시키는 역할을 한다. SV_POSITION 는 특별한 semantic 임을 주목하자. SV 는 system value semantic 을 뜻한다. 이 semantic은 해당 정점 셰이더 출력 성분이 정점의 위치를 담고 있음을 나타낸다. GPU는 절단, 깊이 판정, 레스터화 등등 다른 특성들에는 적용하지 않는 특별한 연산들을 위치에 적용하므로, SV_POSITION semantic을 지정해서 GPU에게 이것이 위치를 담은 출력 성분임을 알려주어야 한다. 반대로 COLOR 는 그냥 응용프로그램이 D3D12_INPUT_ELEMENT_DESC 배열을 통해 지정한 이름이다. system value가 아닌 출력 매개변수 semantic 에는 HLSL의 유효한 식별자이기만 하면 어떤 이름이든 사용 가능하다.

정점 셰이더 첫 줄은 정점 위치에서 $4 \times 4$ 행렬 gWorldViewProj 를 곱해서 정점을 로컬 공간에서 homogeneous clip space(동차 절단 공간) 으로 변환한다.

```hlsl
 // Transform to homogeneous clip space.
 oPosH = mul(float4(iPosL, 1.0f), gWorldViewProj);
```

생성자 구문 float4(iPosL, 1.0f)는 하나의 4차원 벡터를 생성한다. 이는 float4(iPosL.x, iPosL.y, iPosL.z, 1.0f)과 같다. 정점의 위치는 벡터가 아니라 점이기에 넷째 성분을 1로 둔다. float2, float3는 각각 2차원 3차원을 나타낸다. 행렬 gWorldViewProj 는 상수 버퍼라고 부르는 버퍼에 들어 있는 것이다(뒤에 나온다). mul는 HLSL 내장 함수로, 벡터 대 행렬 곱셈을 수행한다. mul은 여러 크기의 행렬 곱셈들에 대해 오버라이드 되어 있다. 

마지막줄은 입력 색상 그대로를 출력 매개변수로 복사한다. 이에 의해 색상이 그대로 파이프라인의 다음 단계에 전달된다.

```hlsl
oColor = iColor;
```

구조체를 사용해서 아래처럼 표현할 수도 있다.

```hlsl
cbuffer cbPerObject : register(b0)
{
 float4x4 gWorldViewProj;
};

struct VertexIn
{
 float3 PosL : POSITION;
 float4 Color : COLOR;
};

struct VertexOut
{
 float4 PosH : SV_POSITION;
 float4 Color : COLOR;
};

VertexOut VS(VertexIn vin)
{
 VertexOut vout;
 // Transform to homogeneous clip space.
 vout.PosH = mul(float4(vin.PosL, 1.0f), gWorldViewProj);
 // Just pass vertex color into the pixel shader.
 vout.Color = vin.Color;

 return vout;
}
```

> geometry shader 를 쓰지 않는다면, 정점 셰이더의 출력은 반드시 SV_POSITION인 hemoegenous clip space 이여야 한다.

> 정점 셰이더는 perspective divde 는 하지 말아야 한다. projection matrix 까지만 수행한다.

<br/>

## Input Layout Description and Input Signature Linking

파이프라인에 공급되는 특성들과 정점 셰이더 매개변수 사이에는 연관 관계가 존재한다. 그러한 관계를 정의하는 것이 input layout description 이다. 파이프라인에 공급된 정점들이 정점 셰이더가 기대하는 모든 입력을 제공하지 못하면 오류가 발생한다. 예를 들어 다음 input layout description 과 vertex struct 는 호환되지 않는다.

```cpp
//--------------
// C++ app code
//--------------
struct Vertex
{
 XMFLOAT3 Pos;
 XMFLOAT4 Color;
};
D3D12_INPUT_ELEMENT_DESC desc[] =
{
 {"POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,
 D3D12_INPUT_PER_VERTEX_DATA, 0},
 {"COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12,
 D3D12_INPUT_PER_VERTEX_DATA, 0}
};
```

```hlsl
//--------------
// Vertex shader
//--------------
struct VertexIn
{
 float3 PosL : POSITION;
 float4 Color : COLOR;
 float3 Normal : NORMAL;
};
struct VertexOut
{
 float4 PosH : SV_POSITION;
 float4 Color : COLOR;
};
VertexOut VS(VertexIn vin) { ... }
```

ID3D12PipelineState 객체를 생성할 때 반드시 input layout description 과 vertex shader 를 함꼐 지정해야 한다. 그러면 D3D는 주어진 두개가 호환되는지 점검한다.

정점 구조체와 input siganature 가 정확히 일치할 필요는 없다. 중요한 것은 정점 셰이더가 기대하는 모든 정보를 제공하느냐이다. 다음 코드는 호환이 된다.

```cpp
//--------------
// C++ app code
//--------------
struct Vertex
{
 XMFLOAT3 Pos;
 XMFLOAT4 Color;
 XMFLOAT3 Normal;
};
D3D12_INPUT_ELEMENT_DESC desc[] =
{
 {"POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,
 D3D12_INPUT_PER_VERTEX_DATA, 0},
 {"COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12,
 D3D12_INPUT_PER_VERTEX_DATA, 0},
 { "NORMAL", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 28,
 D3D12_INPUT_PER_VERTEX_DATA, 0 }
};
```

```hlsl
//--------------
// Vertex shader
//--------------
struct VertexIn
{
 float3 PosL : POSITION;
 float4 Color : COLOR;
};
struct VertexOut
{
 float4 PosH : SV_POSITION;
 float4 Color : COLOR;
};
VertexOut VS(VertexIn vin) { ... }
```

아래는 정점 구조체와 input signature 가 부합하긴 하지만 형식이 다른 경우다.

```cpp
//--------------
// C++ app code
//--------------
struct Vertex
{
 XMFLOAT3 Pos;
 XMFLOAT4 Color;
};
D3D12_INPUT_ELEMENT_DESC desc[] =
{
 {"POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,
 D3D12_INPUT_PER_VERTEX_DATA, 0},
 {"COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12,
 D3D12_INPUT_PER_VERTEX_DATA, 0}
};
```

```hlsl
//--------------
// Vertex shader
//--------------
struct VertexIn
{
 float3 PosL : POSITION;
 int4 Color : COLOR;
};
struct VertexOut
{
 float4 PosH : SV_POSITION;
 float4 Color : COLOR;
};
VertexOut VS(VertexIn vin) { ... }
```

이건 사실 잘못되진 않았다. 입력 레지스터 비트의 reinterpret(재해석)을 허용하기 때문이다. 근데 VC++의 디버그 창은 경고 메시지를 낸다.

<br/>

# Example Pixel Shader

정점 셰이더가 출력한 정점 특성은 레스터화 단계에서 픽셀들을 따라 보간되고, 보관된 결과는 픽셀 셰이더에 입력된다. 아래는 기하 셰이더가 없는 경우에 정점 자료가 픽셀 셰이더에 도달하는 경로를 나타낸다.

![6_5_1](/assets/images/d3d12/6_5_1.png)

정점 셰이더처럼 픽셀 셰이더도 본질적으로 하나의 함수지만, 정점 셰이더와는 다르게 픽셀 단편(fragment)마다 실행된다.

픽셀 셰이더의 주 임무는 주어진 입력으로부터 픽셀 단편의 색상을 계산하는 것이다. 그런데 픽셀 단편이 도중에 기각되어 후면 버퍼까지 도달하지 못할 수도 있음을 주목하자. 예를 들어 픽셀 셰이더에서 절단될 수도 있고, 깊이 값이 더 작은 다른 픽셀 단편에 가려질 수 있고, 스텐실 판정 등 파이프라인의 이후 단계에서 적용되는 판정에 의해 폐기될 수도 있다. 후면 버퍼의 한 픽셀에는 최종적으로 그 픽셀이 될 수 있는 '후보'로서의 픽셀 단편들이 여러 개 존재할 수 있다. 이것이 '픽셀'과는 다른 '픽셀 단편'이라는 용어를 사용하는 이유이다.

> 하드웨어 최적화의 하나로, 파이프라인은 특정 픽셀 단편들을 픽셀 셰이더에 도달하기 전에 폐기할 수 있다. early-Z rejection 이라고 부르는 이 기법은 픽셀의 가려짐이 명백한 픽셀 단편에 대해서는 픽셀 셰이더를 실행하지 않는다. 그런데 이것이 비활성화 될 수 있는데 PS에서 Z의 값이 변경될 수 있다면 비활성화 한다.

```hlsl
cbuffer cbPerObject : register(b0)
{
 float4x4 gWorldViewProj;
};

void VS(float3 iPos : POSITION, 
 float4 iColor : COLOR,
 out float4 oPosH : SV_POSITION,
 out float4 oColor : COLOR)
{
 // Transform to homogeneous clip space.
 oPosH = mul(float4(iPos, 1.0f), gWorldViewProj);
 // Just pass vertex color into the pixel shader.
 oColor = iColor;
}

float4 PS(float4 posH : SV_POSITION, float4 color : COLOR) : SV_Target
{
 return pin.Color;
}
```

이 예제에서 셰이더 함수는 그냥 보간된 색상 값을 돌려준다. 픽셀 셰이더의 입력이 정점 셰이더의 출력과 정확히 일치함을 주목하자. 이것은 필수 조건이다. 한편 픽셀 셰이더의 반환값은 4차원 색상 값이다. 함수 매개변수 목록 다음에 있는 SV_TARGET 이라는 sementic 은 이 함수의 반환 값의 형식이 렌더 대상(render target)의 형식과 일치해야 함을 뜻한다.

이 정점 셰이더와 픽셀 셰이더를 다음과 같이 입력 구조체들과 출력 구조체들을 이용하도록 구현할 수 있다.

```hlsl
cbuffer cbPerObject : register(b0)
{
 float4x4 gWorldViewProj;
};

struct VertexIn
{
 float3 Pos : POSITION;
 float4 Color : COLOR;
};

struct VertexOut
{
 float4 PosH : SV_POSITION;
 float4 Color : COLOR;
};

VertexOut VS(VertexIn vin)
{
 VertexOut vout;
 // Transform to homogeneous clip space.
 vout.PosH = mul(float4(vin.Pos, 1.0f), gWorldViewProj);
 // Just pass vertex color into the pixel shader.
 vout.Color = vin.Color;

 return vout;
}

float4 PS(VertexOut pin) : SV_Target
{
 return pin.Color;
}
```

<br/>

# Constant Buffer

## Creating Constant Buffers

상수 버퍼는 셰이더 프로그램에서 참조하는 자료를 담는 GPU 자원(ID3D12Resource)의 예이다. 텍스처나 기타 버퍼 자원 역시 셰이더 프로그램에서 참조할 수 있다. 이전 정점 셰이더에 다음과 같은 코드가 있었다.

```hlsl
cbuffer cbPerObject : register(b0)
{
 float4x4 gWorldViewProj;
};
```

이 코드는 cbPerObject 라는 cbuffer 객체(상수 버퍼)를 참조한다. 이 예에서 상수 버퍼는 gWorldViewProj 라는 $4 \times 4$ 행렬 하나만 저장한다. 이 행렬은 로컬 공간을 homogeneous clip space 로 변환하기 위해 필요한데, 여기에는 변환하는데 쓰이는 세계행렬, 시야 행렬, 투영 행렬을 하나로 합친 값이다. HLSL 에서 $4 \times 4$ 는 내장 형식 float4x4 로 대표된다. 

정점 버퍼나 인덱스 버퍼와는 달리 상수 버퍼는 CPU가 프레임 당 한 번 갱신하는 것이 일반적이다. 예를 들어 카메라가 매 프레임 이동한다면 프레임마다 상수버퍼를 새 시야행렬로 갱신해야 한다. 따라서 상수버퍼는 기본 힙이 아닌 업로드 힙에 만들어야 한다. 그래야 CPU가 버퍼의 내용을 갱신할 수 있다.

또한, 상수버퍼에는 특별한 하드웨어 요구조건이 있다. 크기가 반드시 최소 하드웨어 할당 크기(256byte)의 배수여야 한다.

같은 종류의 상수 버퍼를 여러개 사용해야 할 경우가 많다. 예를 들어 상수 버퍼 cbPerObject 는 물체마다 달라지는 상수들을 담기에, 만일 장면의 물체가 n개면 이 종류의 상수 버퍼 n개가 필요하다. 다음 코드는 NumElements 개의 상수 버퍼 객체를 담는 하나의 버퍼를 생성하는 방법을 보여준다.

```cpp
struct ObjectConstants
{
 DirectX::XMFLOAT4X4 WorldViewProj = MathHelper::Identity4x4();
};

UINT elementByteSize = d3dUtil::CalcConstantBufferByteSize(sizeof(ObjectConstants));

ComPtr<ID3D12Resource> mUploadCBuffer;

device->CreateCommittedResource(
 &CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_UPLOAD),
 D3D12_HEAP_FLAG_NONE,
 &CD3DX12_RESOURCE_DESC::Buffer(mElementByteSize * NumElements),
 D3D12_RESOURCE_STATE_GENERIC_READ,
 nullptr,
 IID_PPV_ARGS(&mUploadCBuffer));
```

mUploadCBUffer 를 ObjectConstants 형식의 상수 버퍼들을 배열에 담는 버퍼라고 할 수 있다. 어떤 물체를 그릴 때가 되면, 이 버퍼에서 해당 물체를 위한 상수들이 있는 부분 영역을 서술하는 사웃 버퍼 뷰를 파이프라인에 묶는다.

여기서 편의용 함수 d3dUtil::CalcCOnstantBufferByteSize 는 버퍼의 크기를 최소 하드웨어 할당 크기로 (256 byte의 배수로) 됙 ㅔ하는 계산을 수행해준다.

```cpp
static UINT CalcConstantBufferByteSize(UINT byteSize)
{
    // Constant buffers must be a multiple of the minimum hardware
    // allocation size (usually 256 bytes).  So round up to nearest
    // multiple of 256.  We do this by adding 255 and then masking off
    // the lower 2 bytes which store all bits < 256.
    // Example: Suppose byteSize = 300.
    // (300 + 255) & ~255
    // 555 & ~255
    // 0x022B & ~0x00ff
    // 0x022B & 0xff00
    // 0x0200
    // 512
    return (byteSize + 255) & ~255;
}
```

> HLSL 구조체에서 상소 자료의 여분에 바이트를 암묵적으로 채워준다.

```hlsl
// Implicitly padded to 256 bytes.
cbuffer cbPerObject : register(b0)
{
 float4x4 gWorldViewProj;
};

// Explicitly padded to 256 bytes.
cbuffer cbPerObject : register(b0)
{
 float4x4 gWorldViewProj;
 float4x4 Pad0;
 float4x4 Pad1;
 float4x4 Pad1;
};
```

D3D12 는 셰이더 모형(shader model)을 도입했다. 셰이더 모형은 상수 버퍼를 정의하는 또 다른 HLSL 문법을 지원한다.

```hlsl
struct ObjectConstants
{
 float4x4 gWorldViewProj;
 uint matIndex;
};

ConstantBuffer<ObjectConstants> gObjConstants : register(b0);
```

여기선 상수 버퍼에 담을 자료의 형식을 개별적인 구조체로 분리하고, 그 구조체를 이용해 상수 버퍼를 정의한다. 이후 HLSL 에서는 다음처럼 자료 멤버 구문을 이용해 상수 버퍼의 필드에 접근한다.

```hlsl
uint index = gObjConstants.matIndex;
```

<br/>

## Updating Constant Buffer

앞에서 상수 버퍼를 업로드 힙, 즉 D3D12_HEAP_TYPE_UPLOAD 형식의 힙에 생성했기에 CPU에서 상수 버퍼 자원에 자료를 올릴 수 있다. 자료를 올리려면 일단 자원 자료를 가르키는 포인터를 얻어야 하는데 Map 메소드를 호출해준다.

```cpp
ComPtr<ID3D12Resource> mUploadBuffer;
BYTE* mMappedData = nullptr;
mUploadBuffer->Map(0, nullptr, reinterpret_cast<void**>(&mMappedData));
```

이 메소드의 첫 매개변수는 CPU 메모리에 매핑 시키려는 부분 자원의 색인이다. 버퍼의 경우 버퍼 자체가 유일한 부분자원이기에 그냥 0을 지정한다.

둘쨰 매개변수는 대응시킬 메모리의 범위를 서술하는 D3D12_RANGE 구조체의 포인터인데 자원 전체를 대응시키려면 그냥 nullptr 를 지정한다.

출력 매개변수인 셋째 매개변수는 대응된 자료를 가르키는 포인터가 설정된다. 

시스템 메모리에 있는 자료를 상수 버퍼에 복사하려면 다음처럼 memcpy 를 사용한다.

```cpp
memcpy(mMappedData, &data, dataSizeInBytes);
```

복사를 다 했으면 메모리를 해제하기 전에 unmap을 호출하자.

```cpp
if(mUploadBuffer != nullptr)
 mUploadBuffer->Unmap(0, nullptr);

mMappedData = nullptr;
```

Unmap 첫 매개변수는 매핑을 해제할 부분 자원의 색인인데 버퍼는 0이다. 둘쨰 매개변수는 사이즈인데 전체 대응 해제이면 nullptr 이다.

<br/>

## Upload Buffer Helper

업로드 버퍼 관련 기능을 담은 클래스가 있으면 편리할 것이다. 예제 프레임워크의 UploadBuffer.h 에는 업로드 버퍼를 쉽게 다룰 수 있는 UploadBuffer 클래스가 정의되어 있다. 여기선 업로드 버퍼 자원의 생성과 파괴 및 자원의 메모리 대응 및 해제를 처리해준다. 또한, 버퍼의 특정 항목을 갱신하는 CopyData 메소드도 제공한다. 이 클래스를 상수 버퍼에 사용할 떄에는 반드시 생성자의 isConstantBuffer 매개변수에 true 로 지정하는 것을 잊지말자. 그러면 각 상수 버퍼가 256 바이트의 배수가 되도록 적절히 바이트를 채운다.

```cpp
template<typename T>
class UploadBuffer
{
public:
    UploadBuffer(ID3D12Device* device, UINT elementCount, bool isConstantBuffer) : 
        mIsConstantBuffer(isConstantBuffer)
    {
        mElementByteSize = sizeof(T);

        // Constant buffer elements need to be multiples of 256 bytes.
        // This is because the hardware can only view constant data 
        // at m*256 byte offsets and of n*256 byte lengths. 
        // typedef struct D3D12_CONSTANT_BUFFER_VIEW_DESC {
        // UINT64 OffsetInBytes; // multiple of 256
        // UINT   SizeInBytes;   // multiple of 256
        // } D3D12_CONSTANT_BUFFER_VIEW_DESC;
        if(isConstantBuffer)
            mElementByteSize = d3dUtil::CalcConstantBufferByteSize(sizeof(T));

        ThrowIfFailed(device->CreateCommittedResource(
            &CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_UPLOAD),
            D3D12_HEAP_FLAG_NONE,
            &CD3DX12_RESOURCE_DESC::Buffer(mElementByteSize*elementCount),
			D3D12_RESOURCE_STATE_GENERIC_READ,
            nullptr,
            IID_PPV_ARGS(&mUploadBuffer)));

        ThrowIfFailed(mUploadBuffer->Map(0, nullptr, reinterpret_cast<void**>(&mMappedData)));

        // We do not need to unmap until we are done with the resource.  However, we must not write to
        // the resource while it is in use by the GPU (so we must use synchronization techniques).
    }

    UploadBuffer(const UploadBuffer& rhs) = delete;
    UploadBuffer& operator=(const UploadBuffer& rhs) = delete;
    ~UploadBuffer()
    {
        if(mUploadBuffer != nullptr)
            mUploadBuffer->Unmap(0, nullptr);

        mMappedData = nullptr;
    }

    ID3D12Resource* Resource()const
    {
        return mUploadBuffer.Get();
    }

    void CopyData(int elementIndex, const T& data)
    {
        memcpy(&mMappedData[elementIndex*mElementByteSize], &data, sizeof(T));
    }

private:
    Microsoft::WRL::ComPtr<ID3D12Resource> mUploadBuffer;
    BYTE* mMappedData = nullptr;

    UINT mElementByteSize = 0;
    bool mIsConstantBuffer = false;
};
```

일반적으로 물체의 월드 행렬은 장면 안에서 물체가 이동, 회전, 크기가 변하면 바뀌고, 물체의 시야 행렬은 카메라가 이동하거나 회전하면 바뀐다. 투영 행렬은 창의 크기가 변하면 바뀐다. 여기서, 사용자는 마우스를 이용해 카메라를 이동, 회전 할 수 있다. 이를 위해 매 프레임 호출되는 Update 함수에서 세계-시야-투영 행렬(세 행렬을 결합한 행렬)을 새 시야 행렬로 갱신한다.

```cpp
void BoxApp::OnMouseMove(WPARAM btnState, int x, int y)
{
    if((btnState & MK_LBUTTON) != 0)
    {
        // Make each pixel correspond to a quarter of a degree.
        float dx = XMConvertToRadians(0.25f*static_cast<float>(x - mLastMousePos.x));
        float dy = XMConvertToRadians(0.25f*static_cast<float>(y - mLastMousePos.y));

        // Update angles based on input to orbit camera around box.
        mTheta += dx;
        mPhi += dy;

        // Restrict the angle mPhi.
        mPhi = MathHelper::Clamp(mPhi, 0.1f, MathHelper::Pi - 0.1f);
    }
    else if((btnState & MK_RBUTTON) != 0)
    {
        // Make each pixel correspond to 0.005 unit in the scene.
        float dx = 0.005f*static_cast<float>(x - mLastMousePos.x);
        float dy = 0.005f*static_cast<float>(y - mLastMousePos.y);

        // Update the camera radius based on input.
        mRadius += dx - dy;

        // Restrict the radius.
        mRadius = MathHelper::Clamp(mRadius, 3.0f, 15.0f);
    }

    mLastMousePos.x = x;
    mLastMousePos.y = y;
}

void BoxApp::Update(const GameTimer& gt)
{
    // Convert Spherical to Cartesian coordinates.
    float x = mRadius*sinf(mPhi)*cosf(mTheta);
    float z = mRadius*sinf(mPhi)*sinf(mTheta);
    float y = mRadius*cosf(mPhi);

    // Build the view matrix.
    XMVECTOR pos = XMVectorSet(x, y, z, 1.0f);
    XMVECTOR target = XMVectorZero();
    XMVECTOR up = XMVectorSet(0.0f, 1.0f, 0.0f, 0.0f);

    XMMATRIX view = XMMatrixLookAtLH(pos, target, up);
    XMStoreFloat4x4(&mView, view);

    XMMATRIX world = XMLoadFloat4x4(&mWorld);
    XMMATRIX proj = XMLoadFloat4x4(&mProj);
    XMMATRIX worldViewProj = world*view*proj;

	// Update the constant buffer with the latest worldViewProj matrix.
	ObjectConstants objConstants;
    XMStoreFloat4x4(&objConstants.WorldViewProj, XMMatrixTranspose(worldViewProj));
    mObjectCB->CopyData(0, objConstants);
}
```

<br/>

## Constant Buffer Descriptor

자원을 렌더링 파이프라인에 묶으려면 서술자 객체가 필요하다. 상수역시 서술자가 필요하다. 상수 버퍼 서술자는 D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV 형식의 서술자 힙에 담긴다. 이 힙은 상수 버퍼, 셰이더 자원 뷰(SRV), 순서 없는 접근 뷰(UAV) 서술자들을 섞어서 담을 수 있다.

```cpp
D3D12_DESCRIPTOR_HEAP_DESC cbvHeapDesc;
cbvHeapDesc.NumDescriptors = 1;
cbvHeapDesc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_CBV_SRV_UAV;
cbvHeapDesc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_SHADER_VISIBLE;
cbvHeapDesc.NodeMask = 0;
ComPtr<ID3D12DescriptorHeap> mCbvHeap

md3dDevice->CreateDescriptorHeap(&cbvHeapDesc,
 IID_PPV_ARGS(&mCbvHeap));
```

이 코드는 렌더 대상이나 깊이, 스탠실 버퍼 서술자 힙을 생성하는 코드와 비슷하다. 한가지 중요한 차이는 셰이더 프로그램에서 이 서술자들에 접근할 것임을 뜻하는 D3D12_DESCRIPTOR_HEAP_FLAG_VISIBLE 플래그를 지정했다는 점이다. 이번 장의 예제 프로그램은 셰이더 자원이나 순서 없는 접근 뷰를 사용하지 않으며 물체를 하나만 그리기에, 이 힙에는 상수 버퍼 뷰에 대한 서술자 하나만 담으면 된다.

상수 버퍼 뷰를 생성하려면 D3D12_CONSTANT_BUFFER_VIEW_DESC 인스턴스를 채운 후 ID3D12Device::CreateConstantBufferView 를 호출해야 한다.

```cpp
// Constant data per-object.
struct ObjectConstants
{
 XMFLOAT4X4 WorldViewProj = MathHelper::Identity4x4();
};

// Constant buffer to store the constants of n object.
std::unique_ptr<UploadBuffer<ObjectConstants>> mObjectCB = nullptr;
mObjectCB = std::make_unique<UploadBuffer<ObjectConstants>>(
 md3dDevice.Get(), n, true);
UINT objCBByteSize = d3dUtil::CalcConstantBufferByteSize(sizeof(ObjectConstants));

// Address to start of the buffer (0th constant buffer).
D3D12_GPU_VIRTUAL_ADDRESS cbAddress = mObjectCB->Resource()->GetGPUVirtualAddress();

// Offset to the ith object constant buffer in the buffer.
int boxCBufIndex = i;
cbAddress += boxCBufIndex*objCBByteSize;
D3D12_CONSTANT_BUFFER_VIEW_DESC cbvDesc;
cbvDesc.BufferLocation = cbAddress;
cbvDesc.SizeInBytes = d3dUtil::CalcConstantBufferByteSize(sizeof(ObjectConstants));
md3dDevice->CreateConstantBufferView(
 &cbvDesc,
 mCbvHeap->GetCPUDescriptorHandleForHeapStart());
```

D3D12_CONSTANT_BUFFER_VIEW_DESC 구조체는 상수 버퍼 자원 중 HLSL 상수 버퍼 구조체에 묶일 부분을 서술한다. 앞서 이야기 했듯이, 상수 버퍼에는 물체당 상수 자료 n개의 배열을 저장한다. BufferLocation 과 SizeInBytes 를 적절히 지정해서 i 번쨰 물체의 상수 자료에 대한 뷰를 얻을 수 있다. 하드웨어 제약 때문에 D3D12_CONSTANT_BUFFER_VIEW_DESC::SizeInBytes 와 D3D12_CONSTANT_BUFFER_VIEW_DESC::OffsetInBytes 는 256바이트의 배수여야 한다. 이 멤버에 64를 지정하면 아래와 같은 디버그 에러가 나온다.

```text
D3D12 ERROR: ID3D12Device::CreateConstantBufferView: SizeInBytes of 64
is invalid. Device requires SizeInBytes be a multiple of 256.

D3D12 ERROR: ID3D12Device:: CreateConstantBufferView: OffsetInBytes of
64 is invalid. Device requires OffsetInBytes be a multiple of 256.
```

<br/>

## Root Signature and Descriptor Tables

셰이더 프로그램들은 특정 종류의 자원들이 렌더링 파이프라인에 묶인 상태에서 그리기 호출이 실행되었다고 기대한다. 자원들은 특정 레지스터에 묶이며, 셰이더 프로그램들은 그 슬롯들을 통해서 자원에 접근한다. 예를 들어 이전의 정점 셰이더와 픽셀 셰이더는 상수 버퍼 하나가 레지스터 b0에 묶여 있다고 기대한다.

```hlsl
// Texture resource bound to texture register slot 0.
Texture2D gDiffuseMap : register(t0);

// Sampler resources bound to sampler register slots 0-5.
SamplerState gsamPointWrap : register(s0);
SamplerState gsamPointClamp : register(s1);
SamplerState gsamLinearWrap : register(s2);
SamplerState gsamLinearClamp : register(s3);
SamplerState gsamAnisotropicWrap : register(s4);
SamplerState gsamAnisotropicClamp : register(s5);

// cbuffer resource bound to cbuffer register slots 0-2
cbuffer cbPerObject : register(b0)
{
 float4x4 gWorld;
 float4x4 gTexTransform;
};

// Constant data that varies per material.
cbuffer cbPass : register(b1)
{
 float4x4 gView;
 float4x4 gProj;
 [...] // Other fields omitted for brevity.
};

cbuffer cbMaterial : register(b2)
{
 float4 gDiffuseAlbedo;
 float3 gFresnelR0;
 float gRoughness;
 float4x4 gMatTransform;
};
```

root signature 는 그리기 호출 전에 응용 프로그램이 반드시 렌더링 파이프라인에 묶어야 하는 자원들이 무엇이고 그 자원들이 입력 레지스터 들에 어떻게 대응되는 지를 정의한다. 루트 시그니쳐는 반드시 그리기 호출에 쓰이는 셰이더들과 호환되어야 한다. 루트 시그니처의 유효성은 파이프라인의 상태 객체를 생성할 때 검증된다. 그리기 호출마다 서로 다른 셰이더 프로그램들을 사용할 수 있으며, 그런 경우 루트 시그니처도 달라야 한다.

D3D에서 루트 시그니처를 대표하는 인터페이스는 ID3D12RootSignature 이다. 루트 시그니처는 주어진 드로우 콜에서 셰이더들이 기대하는 자원들을 서술하는 루트 매개변수드르이 배열로 정의된다. 여기서 루트 파라메터는 하나의 루트 콘스탄트나 루트 디스크립터일 수 있고 디스크립터 테이블일 수도 있다. 여기서는 디스크립터 테이블만 사용해본다. 디스크립터 테이블은 디스크립터 힙 안에 있는 연속된 디스크립터들의 구간을 지정한다.

아래는 루트 파라메터 하나로 된 루트 시그니처를 생성한다. 그 루트 파라메터는 CBV(Constant Buffer View) 하나를 담기에 충분한 크기의 디스크립터 테이블이다.

```cpp
// Root parameter can be a table, root descriptor or root constants.
CD3DX12_ROOT_PARAMETER slotRootParameter[1];

// Create a single descriptor table of CBVs.
CD3DX12_DESCRIPTOR_RANGE cbvTable;
cbvTable.Init(
 D3D12_DESCRIPTOR_RANGE_TYPE_CBV,
 1, // Number of descriptors in table
 0);// base shader register arguments are bound to for this root parameter

slotRootParameter[0].InitAsDescriptorTable(
 1, // Number of ranges
 &cbvTable); // Pointer to array of ranges

// A root signature is an array of root parameters.
CD3DX12_ROOT_SIGNATURE_DESC rootSigDesc(1, slotRootParameter, 0,
 nullptr,
 D3D12_ROOT_SIGNATURE_FLAG_ALLOW_INPUT_ASSEMBLER_INPUT_LAYOUT);

// create a root signature with a single slot which points to a
// descriptor range consisting of a single constant buffer.
ComPtr<ID3DBlob> serializedRootSig = nullptr;
ComPtr<ID3DBlob> errorBlob = nullptr;
HRESULT hr = D3D12SerializeRootSignature(&rootSigDesc,
 D3D_ROOT_SIGNATURE_VERSION_1,
 serializedRootSig.GetAddressOf(),
 errorBlob.GetAddressOf());
ThrowIfFailed(md3dDevice->CreateRootSignature(
 0,
 serializedRootSig->GetBufferPointer(),
 serializedRootSig->GetBufferSize(),
 IID_PPV_ARGS(&mRootSignature)));
```

내용이 다양하지만 여기서는 CBV 하나(상수 버퍼 레지스터 0에 묶이는, 즉 HLSL 코드의 register(b0)에 대응되는)를 담은 디스크립터 테이블을 기대하는 루트 파라메터를 생성한다는 점을 이해하고 넘어가자.

```cpp
CD3DX12_ROOT_PARAMETER slotRootParameter[1];
CD3DX12_DESCRIPTOR_RANGE cbvTable;

cbvTable.Init(
 D3D12_DESCRIPTOR_RANGE_TYPE_CBV, // table type
 1, // Number of descriptors in table
 0);// base shader register arguments are bound to for this root parameter

slotRootParameter[0].InitAsDescriptorTable(
 1, // Number of ranges
 &cbvTable); // Pointer to array of ranges
```

루트 시그니처는 응용 프로그램이 렌더링 파이프라인에 묶을 자원들을 정의하기만 한다. 루트 시그니처가 실제로 자원들을 묶진 않는다. 커맨드 리스트를 통해 루트 시그니처를 설정한 후에는 ID3D12GraphicsCommandList::SetGraphicsRootDescriptorTable 을 호출해 디스크립터 테이블을 파이프라인에 묶는다.

```cpp
void ID3D12GraphicsCommandList::SetGraphicsRootDescriptorTable(
 UINT RootParameterIndex,
 // 설정하고자 하는 루트 디스크립터 색인
 D3D12_GPU_DESCRIPTOR_HANDLE BaseDescriptor);
 // 설정하고자 하는 디스크립터 테이블의 첫 디스크립터에 해당하는 디스크립터의 핸들.
```

아래 코드는 루트 시그니처와 CBV 힙을 명령 목록에 설정하고, 파이프라인에 묶을 자원들을 지정하는 디스크립터 테이블을 설정한다.

```cpp
mCommandList->SetGraphicsRootSignature(mRootSignature.Get());
ID3D12DescriptorHeap* descriptorHeaps[] = { mCbvHeap.Get() };
mCommandList->SetDescriptorHeaps(_countof(descriptorHeaps), descriptorHeaps);

// Offset the CBV we want to use for this draw call.
CD3DX12_GPU_DESCRIPTOR_HANDLE cbv(mCbvHeap->GetGPUDescriptorHandleForHeapStart());
cbv.Offset(cbvIndex, mCbvSrvUavDescriptorSize);
mCommandList->SetGraphicsRootDescriptorTable(0, cbv);
```

<br/>

# 셰이더의 컴파일

D3D에서 셰이더 프로그램은 먼저 이식성 있는 바이트 코드로 컴파일 되야 한다. 그래픽 드라이버는 그 바이트코드를 다시 시스템의 GPU에 맞춰 최적의 네이티브 명령들로 컴파일 한다. 런타임에서 다음과 같은 함수를 이용해서 셰이더 컴파일을 할 수 있다.


```cpp
HRESULT D3DCompileFromFile(
 LPCWSTR pFileName,
 // 컴파일할 HLSL 소스코드를 담은 .hlsl 파일 이름
 const D3D_SHADER_MACRO *pDefines,
 // 고급 옵션. 추가참조
 ID3DInclude *pInclude,
 // 고급 옵션. 추가참조
 LPCSTR pEntrypoint,
 // 셰이더 프로그램의 진입점 함수 이름. 하나의 hlsl 파일에 여러 개의 셰이더 프로그램이 있을 수 있으니 컴파일할 특정 셰이더의 진입점을 명시해 줘야 한다.
 LPCSTR pTarget,
 // 사용할 셰이더프로그램의 종류와 대상 버전을 나타내는 문자열. 여기선 5.0, 5.1을 사용
 UINT Flags1,
 // 셰이더 코드의 세부적인 컴파일 방식을 제어하는 플래그. 다양하지만 여기선 다음 두개를 사용한다.
 // D3DCOMPILE_DEBUG : 셰이더를 디버그 모두에서 컴파일
 // D3DCOMPILE_SKIP_OPTIMIZATION : 최적화 생략
 UINT Flags2,
 // effect에 관한 고급 옵션
 ID3DBlob **ppCode,
 // 컴파일된 셰이더 목적 바이트코드를 담은 ID3DBlob 구조체의 포인터
 ID3DBlob **ppErrorMsgs);
 // 컴파일 오류가 발생한 경우 오류 메시지 문자열을 담은 ID3DBlob 구조체의 포인터를 이 매개변수를 통해 돌려준다.
```

ID3DBlob는 범용 메모리 버퍼를 나타내는 형식으로, 아래 두 메소드를 제공한다.

* LPVOID GetBufferPointer : 버퍼를 가르키는 void* 포인터를 돌려준다. 그 블록에 담긴 객체를 실제로 쓰려면 적절한 형식으로 캐스팅 해야한다.
* SIZE_T GetBufferSize : 버퍼의 크기를 돌려준다.

예제 프레임워크는 셰이더 프로그램을 쉽게 하기위해 다음 보조함수를 제공한다.

```cpp
ComPtr<ID3DBlob> d3dUtil::CompileShader(
	const std::wstring& filename,
	const D3D_SHADER_MACRO* defines,
	const std::string& entrypoint,
	const std::string& target)
{
	UINT compileFlags = 0;
#if defined(DEBUG) || defined(_DEBUG)  
	compileFlags = D3DCOMPILE_DEBUG | D3DCOMPILE_SKIP_OPTIMIZATION;
#endif

	HRESULT hr = S_OK;

	ComPtr<ID3DBlob> byteCode = nullptr;
	ComPtr<ID3DBlob> errors;
	hr = D3DCompileFromFile(filename.c_str(), defines, D3D_COMPILE_STANDARD_FILE_INCLUDE,
		entrypoint.c_str(), target.c_str(), compileFlags, 0, &byteCode, &errors);

	if(errors != nullptr)
		OutputDebugStringA((char*)errors->GetBufferPointer());

	ThrowIfFailed(hr);

	return byteCode;
}
```

이 함수의 호출 예시이다.

```cpp
ComPtr<ID3DBlob> mvsByteCode = nullptr;
ComPtr<ID3DBlob> mpsByteCode = nullptr;
mvsByteCode = d3dUtil::CompileShader(L"Shaders\\color.hlsl",
 nullptr, "VS", "vs_5_0");
mpsByteCode = d3dUtil::CompileShader(L"Shaders\\color.hlsl",
 nullptr, "PS", "ps_5_0");
```

HLSL의 오류 메시지와 경고 메시지는 ppErrorMsgs 매개변수를 통해 반환된다.

셰이더를 컴파일한다고 셰이더가 렌더링 파이프라인에 묶이지는 않는다.

<br/>

## 오프라인 컴파일

셰이더를 실행 시점에 컴파일 하지 않고 오프라인에서 개별적인 단계로 컴파일 할 수 있다. 오프라인 컴파일이 필요한 이유를 몇가지 들면 아래와 같다.

* 복잡한 셰이더는 컴파일이 오래 걸릴 수 있다. 셰이더를 오프라인에서 컴파일하면 게임의 로딩 시간이 빨라진다.
* 셰이더 컴파일 오류들은 빌드 과정에서 일찍 점검하는 편이 편하다.
* Windows 8 스토어 앱은 반드시 오프라인 컴파일을 이용해야 한다.

컴파일된 셰이더를 담은 파일의 확장자로는 .cso(compiled shader object)를 사용하는 것이 관례이다.

셰이더를 오프라인에서 컴파일 할 때는 DirectX에 포함된 FXC 도구를 사용한다. 이것은 커맨드라인 유틸리티다. 예를 들어 color.hsls 에 담긴, 진입점이 VS인 정점 셰이더와 진입점이 PS인 픽셀 셰이더를 디버그 모드로 각각 컴파일 하려면 아래와 같은 명령들을 실행하면 된다.

```bat
fxc "color.hlsl" /Od /Zi /T vs_5_0 /E "VS" /Fo "color_vs.cso" /Fc "color_vs.asm"
fxc "color.hlsl" /Od /Zi /T ps_5_0 /E "PS" /Fo "color_ps.cso" /Fc "color_ps.asm"
```

다음은 릴리즈로 컴파일하는 명령이다.

```bat
fxc "color.hlsl" /T vs_5_0 /E "VS" /Fo "color_vs.cso" /Fc "color_vs.asm"
fxc "color.hlsl" /T ps_5_0 /E "PS" /Fo "color_ps.cso" /Fc "color_ps.asm"
```

| 명령줄 옵션 | 설명 |
| --------- | ---------|
| /Od | 최적화 비활성화 |
| /Zi | 디버그 정보 활성화 |
| /T <문자열> | 셰이더의 종류와 대상 버전 |
| /E <문자열> | 셰이더 진입점 |
| /Fo <문자열> | 컴파일된 셰이더 바이트코드 목적 파일 |
| /Fc <문자열> | 어셈블리 코드 목록을 출력 |

구문 오류가 있는 셰이더 프로그램얼 컴파일하면 FXC는 콘솔 창에 오류/경고 메시지를 출력한다.

예를 들어 color.hlsl 파일에서 다음처럼 변수 이름을 틀리게 적었다고 하자.

```text
// Should be gWorldViewProj, not worldViewProj!
vout.PosH = mul(float4(vin.Pos, 1.0f), worldViewProj);
```

이 실수 하나에 콘솔창에 많은 에러메시지가 나온다. 핵심은 제일 처음 나온 에러이다.

```text
color.hlsl(29,42-54): error X3004: undeclared identifier ‘worldViewProj’
color.hlsl(29,14-55): error X3013: ‘mul’: no matching 2 parameter intrinsic function
color.hlsl(29,14-55): error X3013: Possible intrinsic functions are:
color.hlsl(29,14-55): error X3013: mul(float|half...
```

이런 에러 메시지는 런타임보다 컴파일 타임에서 얻는 게 더 편하다.

이 경우 이미 컴파일 했기에 런타임에서 컴파일할 필요가 없다. 대신, .cso 파일에 담긴 셰이더 바이트코드를 응용 프로그램으로 적재해야 한다. 프로임워크에서는 C++ 표준 파일 입출력을 사용한 아래 함수를 제공한다.

```cpp
ComPtr<ID3DBlob> d3dUtil::LoadBinary(const std::wstring& filename)
{
    std::ifstream fin(filename, std::ios::binary);

    fin.seekg(0, std::ios_base::end);
    std::ifstream::pos_type size = (int)fin.tellg();
    fin.seekg(0, std::ios_base::beg);

    ComPtr<ID3DBlob> blob;
    ThrowIfFailed(D3DCreateBlob(size, blob.GetAddressOf()));

    fin.read((char*)blob->GetBufferPointer(), size);
    fin.close();

    return blob;
}
//...

ComPtr<ID3DBlob> mvsByteCode = d3dUtil::LoadBinary("LShaders\\color_vs.cso");
ComPtr<ID3DBlob> mpsByteCode = d3dUtil::LoadBinary("LShaders\\color_ps.cso");
```

<br/>

## 어셈블리 코드 생성

실행시 /Fc 옵션을 주면 FXC는 이식성 있는 어셈블리 코드를 생성한다. 종종 셰이더의 어셈블리 코드를 출력해서 셰이더 명령 개수를 확인하거나 어떤 종류의 코드가 생성되었는지 살펴보면 도움이 된다. 기대한 것 과는 다른 코드가 생성되는 경우도 있다. 예를 들어 HLSL 코드에서 어떤 조건문을 사용했다면 어셈블리 코드에 분기 명령이 있을것이라 생각하기 쉽다. programmable GPU의 초창기에는 셰이더에서 분기 명령을 사용하는 비용이 높았어서 컴파일러가 조건문을 평평하게(floating)만들어 분기명령을 제거하기도 했다. 흔히 쓰이는 기법은 두 분기 경로를 모두 평가하고 적절히 보간해서 정확한 답을 생성하는 것이었다.

| 조건문이 포함된 코드 | floating 된 코드 |
| ---| --- |
| float x = 0; <br/> //s == 1 or s == 0 <br/> if(s) <br/> x = sqrt(y); <br/> else <br/> x = 2*y; | float a = 2 * y; <br/> float b = sqrt(y); <br/> float x = a + s * (b - a); <br/> // s == 1: x = a + b - a = b = sqrt(y) <br/> // s == 0: x = a + 0 * (b - a) = a = 2 * y <br/> |

이처럼, floating 된 코드도 원래의 코드와 같은 결과를 낸다. 하지만 어셈블리 코드를 보지 않고선, floating이 적용 됬는지를 알 수가 없다. 어쨋든, 종종 어셈블리 코드를 살펴보면서 코드가 어떻게 생성되는지 확인하면 유익하다.

```text
//
// Generated by Microsoft (R) HLSL Shader Compiler 6.4.9844.0
//
//
// Buffer Definitions:
//
// cbuffer cbPerObject
// {
//
// float4x4 gWorldViewProj; // Offset: 0 Size: 64
//
// }
//
//
// Resource Bindings:
//
// Name Type Format Dim Slot Elements
// ------------------------------ ---------- ------- ----------- ----
// cbPerObject cbuffer NA NA 0 1
//
//
//
// Input signature:
//
// Name Index Mask Register SysValue Format Used
// -------------------- ----- ------ -------- -------- ------- ------
// POSITION 0 xyz 0 NONE float xyz
// COLOR 0 xyzw 1 NONE float xyzw
//
//
// Output signature:
//
// Name Index Mask Register SysValue Format Used
// -------------------- ----- ------ -------- -------- ------- ------
// SV_POSITION 0 xyzw 0 POS float xyzw
// COLOR 0 xyzw 1 NONE float xyzw
//
vs_5_0
dcl_globalFlags refactoringAllowed | skipOptimization
dcl_constantbuffer cb0[4], immediateIndexed
dcl_input v0.xyz
dcl_input v1.xyzw
dcl_output_siv o0.xyzw, position
dcl_output o1.xyzw
dcl_temps 2
//
// Initial variable locations:
// v0.x <- vin.PosL.x; v0.y <- vin.PosL.y; v0.z <- vin.PosL.z;
// v1.x <- vin.Color.x; v1.y <- vin.Color.y; v1.z <- vin.Color.z; v1.w<- vin.Color.w;
// o1.x <- <VS return value>.Color.x;
// o1.y <- <VS return value>.Color.y;
// o1.z <- <VS return value>.Color.z;
// o1.w <- <VS return value>.Color.w;
// o0.x <- <VS return value>.PosH.x;
// o0.y <- <VS return value>.PosH.y;
// o0.z <- <VS return value>.PosH.z;
// o0.w <- <VS return value>.PosH.w
//

#line 29 "color.hlsl"
mov r0.xyz, v0.xyzx
mov r0.w, l(1.000000)
dp4 r1.x, r0.xyzw, cb0[0].xyzw // r1.x <- vout.PosH.x
dp4 r1.y, r0.xyzw, cb0[1].xyzw // r1.y <- vout.PosH.y
dp4 r1.z, r0.xyzw, cb0[2].xyzw // r1.z <- vout.PosH.z
dp4 r1.w, r0.xyzw, cb0[3].xyzw // r1.w <- vout.PosH.w

#line 32
mov r0.xyzw, v1.xyzw // r0.x <- vout.Color.x; r0.y <- vout.Color.y;
 // r0.z <- vout.Color.z; r0.w <- vout.Color.w
mov o0.xyzw, r1.xyzw
mov o1.xyzw, r0.xyzw
ret
// Approximately 10 instruction slots used
```

<br/>

# Resterizer State

렌더링 파이프라인의 많은 부분이 프로그래밍 가능하지만, 일부는 구성(설정)만 가능하다. 렌더링 파이프라인의 레스터라이커 스테이지 단계가 그러하다. 레스터라이저 스테이지는 rasterizer state 를 통해서 구성된다. 이 상태를 대표하는 것은 D3D12_RASTERIZER_DESC 이다.


```cpp
typedef struct D3D12_RASTERIZER_DESC {
 D3D12_FILL_MODE FillMode; // Default: D3D12_FILL_SOLID
 // 와이어 프레임 렌더링을 위해선 D3D12_FILL_WIREFRAME을, 면의 속을 채운 렌더링을 위해선 D3D12_FILL_SOLID 를 지정한다.
 D3D12_CULL_MODE CullMode; // Default: D3D12_CULL_BACK
 // 선별을 끄려면 D3D12_CULL_NONE, 후면 선별 D3D12_CULL_BACK, 전면 선별 D3D12_CULL_FRONT
 BOOL FrontCounterClockwise; // Default: false
 // 시계방향 정점을 전면으로 취급하려면 false, 반대는 true
 INT DepthBias; // Default: 0
 FLOAT DepthBiasClamp; // Default: 0.0f
 FLOAT SlopeScaledDepthBias; // Default: 0.0f
 BOOL DepthClipEnable; // Default: true
 BOOL ScissorEnable; // Default: false
 // 가위 판정을 활성화하려면 treu, 반대는 false
 BOOL MultisampleEnable; // Default: false
 BOOL AntialiasedLineEnable; // Default: false
 UINT ForcedSampleCount; // Default: 0
 // Default: D3D12_CONSERVATIVE_RASTERIZATION_MODE_OFF
 D3D12_CONSERVATIVE_RASTERIZATION_MODE ConservativeRaster;
} D3D12_RASTERIZER_DESC;
```

다음은 와이어프레임 모드를 키고, 후면 선별을 끄는 상태 설정 방법이다.

```cpp
CD3DX12_RASTERIZER_DESC rsDesc(D3D12_DEFAULT);
rsDesc.FillMode = D3D12_FILL_WIREFRAME;
rsDesc.CullMode = D3D12_CULL_NONE;
```

CD3DX12_RASTERIZER_DESC 는 D3D12_RASTERIZER_DESC 를 상속해서 몇 가지 편의용 생성자들을 추가한 보조 클래스이다. 이 클래스는 CD3D12_DEFAULT 형식의 객체를 받는 생성자를 제공하는데, 이 생성자는 레스터라이저 스테이트의 멤버를 모두 기본값으로 초기화한다.

CD3D12_DEFAULT 는 오버라이드를 위해 도입한 빈 구조체이다.

```cpp
struct CD3D12_DEFAULT {};
extern const DECLSPEC_SELECTANY CD3D12_DEFAULT D3D12_DEFAULT;
```

<br/>

# Pipeline State object

아직 객체들, 자원들을 실제로 사용하기 위해 렌더링 파이프라인에 묶는 방법을 얘기하지 않았다. 렌더링 파이프라인의 상태를 제어하는 대부분의 객체는 파이프라인 상태(pipeline state object, PSO) 라고 부르는 aggregate 를 통해서 지정된다. D3D에서 PSO를 대표하는 인터페이스는 ID3D12PipelineState 이다. PSO를 생성하려면 우선 파이프라인 상태를 서술하는 D3D12_GRAPHICS_PIPELINE_STATE_DESC 구조체의 인스턴스를 채워야 한다.

```cpp
typedef struct D3D12_GRAPHICS_PIPELINE_STATE_DESC
{
 ID3D12RootSignature *pRootSignature;
 // 이 PSO오 ㅏ함께 묶을 루트 시그니처를 가르키는 포인터. 루트 시그니처는 반드시 이 PSO로 묶는 셰이더들과 호환되야 한다.
 D3D12_SHADER_BYTECODE VS;
 // 묶을 정점 셰이더를 서술하는 D3D12_SHADER_BYTECODE 구조체.
 // typedef struct D3D12_SHADER_BYTECODE {
 // const BYTE *pShaderBytecode;
 // SIZE_T BytecodeLength;
 // } D3D12_SHADER_BYTECODE;
 D3D12_SHADER_BYTECODE PS;
 // 묶을 픽셀 셰이더
 D3D12_SHADER_BYTECODE DS;
 // 묶을 도메인 셰이더
 D3D12_SHADER_BYTECODE HS;
 // 묶을 헐 셰이더
 D3D12_SHADER_BYTECODE GS;
 // 묶을 지오매트리 셰이더
 D3D12_STREAM_OUTPUT_DESC StreamOutput;
 // 스트림 출력이라는 고급 기법에 쓰인다.
 D3D12_BLEND_DESC BlendState;
 // 혼합 방식을 서술하는 혼합 상태를 지정한다. 일단 기본값인 CD3DX12_BLEND_DESC를 지정한다.
 UINT SampleMask;
 // 멀티 샘플링은 최대 32개의 표본을 취할 수 있다.
 D3D12_RASTERIZER_DESC RasterizerState;
 // 레스터라이즈 단계를 구성하는 레스터화 상태
 D3D12_DEPTH_STENCIL_DESC DepthStencilState;
 // 깊이*스탠실 판정을 구성하는 상태를 지정. 기본값인 CD3D12_DEPtH_STENCIL_DESC를 지정
 D3D12_INPUT_LAYOUT_DESC InputLayout;
 // input layer 를 서술하는 구조체를 지정.
 // D3D12_INPUT_ELEMENT_DESC 원소들의 배열과 그 배열의 원소 갯수로 구성되어 있다.
 D3D12_PRIMITIVE_TOPOLOGY_TYPE PrimitiveTopologyType;
 // 기본 도형 위상 구조의 종류를 지정한다.
 UINT NumRenderTargets;
 // 동시에 사용할 렌더 대상 갯수
 DXGI_FORMAT RTVFormats[8];
 // 렌더 대상 형식들. 동시에 여러 렌더 대상에 장면을 그릴 수 있도록 렌더 대상 형식들의 배열을 지정한다. PSO 와 함꼐 사용할 렌더 대상의 설정들과 부합해야 한다.
 DXGI_FORMAT DSVFormat;
 // 깊이*스탠실 버퍼의 형식. 이 PSO 와 함꼐 사용할 깊이*스탠실 버퍼의 설정과 부합해야 한다.
 DXGI_SAMPLE_DESC SampleDesc;
 // 멀티 샘플링의 표본 갯수와 품질 수준. PSO 설정과 부합해야 한다.
} D3D12_GRAPHICS_PIPELINE_STATE_DESC;
```

D3D12_GRAPHICS_PIPELINE_STATE_DESC 인스턴스를 채운 후엔 ID3D12Device::CreateGrahpicsPipelineState 메소드를 이용해 ID3D12PipelineState 객체를 생성한다.

```cpp
ComPtr<ID3D12RootSignature> mRootSignature;
std::vector<D3D12_INPUT_ELEMENT_DESC> mInputLayout;
ComPtr<ID3DBlob> mvsByteCode;
ComPtr<ID3DBlob> mpsByteCode;
//...
D3D12_GRAPHICS_PIPELINE_STATE_DESC psoDesc;
ZeroMemory(&psoDesc, sizeof(D3D12_GRAPHICS_PIPELINE_STATE_DESC));
psoDesc.InputLayout = { mInputLayout.data(), (UINT)mInputLayout.size()
};
psoDesc.pRootSignature = mRootSignature.Get();
psoDesc.VS =
{
 reinterpret_cast<BYTE*>(mvsByteCode->GetBufferPointer()), 
  mvsByteCode->GetBufferSize()
};
psoDesc.PS =
{
 reinterpret_cast<BYTE*>(mpsByteCode->GetBufferPointer()),
 mpsByteCode->GetBufferSize()
};
psoDesc.RasterizerState = CD3D12_RASTERIZER_DESC(D3D12_DEFAULT);
psoDesc.BlendState = CD3D12_BLEND_DESC(D3D12_DEFAULT);
psoDesc.DepthStencilState = CD3D12_DEPTH_STENCIL_DESC(D3D12_DEFAULT);
psoDesc.SampleMask = UINT_MAX;
psoDesc.PrimitiveTopologyType = D3D12_PRIMITIVE_TOPOLOGY_TYPE_TRIANGLE;
psoDesc.NumRenderTargets = 1;
psoDesc.RTVFormats[0] = mBackBufferFormat;
psoDesc.SampleDesc.Count = m4xMsaaState ? 4 : 1;
psoDesc.SampleDesc.Quality = m4xMsaaState ? (m4xMsaaQuality - 1) : 0;
psoDesc.DSVFormat = mDepthStencilFormat;

ComPtr<ID3D12PipelineState> mPSO;
md3dDevice->CreateGraphicsPipelineState(&psoDesc, IID_PPV_ARGS(&mPSO)));
```

ID3D12PipelineState에 상당히 많은 상태가 들어있다. 이 모든 객체를 하나의 집합체로서 렌더링 파이프라인에 지정하는 이유는 성능 대문이다. 이 모든 것을 하나의 집합체로 지정하는 덕분에 D3D에서는 모든 상태가 호환되는지 미리 검증 가능하고, 드라이버는 하드웨어 상태의 프로그래밍을 위한 모든 코드를 미리 생성 가능해진다. D3D11 에서는 이 렌더 상태 조각들을 개별적으로 설정했었다. 근데 이 렌더 상태 조각들은 서로 연관되어 있어 한 조각의 상태가 바뀌면, 의존하는 다른 조각을 윟 ㅐ드라이버가 하드웨어를 다시 프로그래밍 해야 할 수 있다. 이러면 파이프라인을 구성하는 과정에서 다수의 상태 조각을 변경하면 드라이버가 하드웨어를 중복해서 다시 프로그래밍 하기 쉽다. 이러한 중복을 피하기 위한 기법중 하나가 하드웨어 프로그래밍을 모든 파이프라인 상태가 알려진 시점인 드로우 시점으로 미루는 것이다. 그러나 하드웨어 프로그램을 이런식으로 미루려면 추가적인 관리작업을 수행해야 한다.

D3D12 의 새 모형에서는 응용 프로그램이 파이프라인 상태의 대부분을 하나의 집합체로 지정함으로써, 드라이버는 파이프라인의 프로그래밍에 필요한 모든 코드를 초기화 시점에서 생성할 수 있다.

PSO가 모든 렌더 상태를 포함하나는 것은 아니다. 뷰포트나 가위 직사각형 같은 상태는 PSO와는 따로 지정한다. 그런 상태들은 다른 파이프라인 상태들과 독립적으로 지정해도 비효율적이지 않기에 PSO에 포함해도 별 이득이 없다.

D3D는 하나의 state machine 이다. D3D에는 명시적으로 변경하지 않는 한 그대로 남아있는 것들이 많다. 한 장면에서 여러 부류의 물체들을 각자 다른 PSO를 이용해서 그린다면, 코드의 구조를 아래처럼 짜야한다.

```cpp
// Reset specifies initial PSO.
mCommandList->Reset(mDirectCmdListAlloc.Get(), mPSO1.Get())
/* ...draw objects using PSO 1... */

// Change PSO
mCommandList->SetPipelineState(mPSO2.Get());
/* ...draw objects using PSO 2... */

// Change PSO
mCommandList->SetPipelineState(mPSO3.Get());
/* ...draw objects using PSO 3... */
```

다른 말로 하면, 어떤 PSO를 커맨드 리스트에 묶었으면 다른 PSO가 묶이기 전까진 그 PSO가 계속 적용된다.

<br/>

# GEOMETRY HELPER STRUCTURE

여기서는 하나의 기하구조 그룹을 정의하는 정점 버퍼와 인덱스 버퍼를 한데 엮는 편리한 보조 구조체에 대해서 설명한다. 이 구조체는 실제 정점 자료와 인덱스 자료를 시스템 메모리에 유지해서 CPU가 그 자료를 언제라고 읽을 수 있게 하는 역할도 한다. 선택이나 충돌 검출을 위해서는 CPU가 기하구조 자료에 접근해야 한다. 더 나아가 이 구조체는 버퍼 형식이나 stride 같은 정점 버퍼와 인덱스 버퍼의 주요 속성들도 담아주며, 버퍼에 대한 뷰를 돌려주는 메소드도 제공한다. 프레임워크의 MeshGeometry 가 바로 그러한 구조체이다.

```cpp
// Defines a subrange of geometry in a MeshGeometry.  This is for when multiple
// geometries are stored in one vertex and index buffer.  It provides the offsets
// and data needed to draw a subset of geometry stores in the vertex and index 
// buffers so that we can implement the technique described by Figure 6.3.
struct SubmeshGeometry
{
	UINT IndexCount = 0;
	UINT StartIndexLocation = 0;
	INT BaseVertexLocation = 0;

    // Bounding box of the geometry defined by this submesh. 
    // This is used in later chapters of the book.
	DirectX::BoundingBox Bounds;
};

struct MeshGeometry
{
	// Give it a name so we can look it up by name.
	std::string Name;

	// System memory copies.  Use Blobs because the vertex/index format can be generic.
	// It is up to the client to cast appropriately.  
	Microsoft::WRL::ComPtr<ID3DBlob> VertexBufferCPU = nullptr;
	Microsoft::WRL::ComPtr<ID3DBlob> IndexBufferCPU  = nullptr;

	Microsoft::WRL::ComPtr<ID3D12Resource> VertexBufferGPU = nullptr;
	Microsoft::WRL::ComPtr<ID3D12Resource> IndexBufferGPU = nullptr;

	Microsoft::WRL::ComPtr<ID3D12Resource> VertexBufferUploader = nullptr;
	Microsoft::WRL::ComPtr<ID3D12Resource> IndexBufferUploader = nullptr;

    // Data about the buffers.
	UINT VertexByteStride = 0;
	UINT VertexBufferByteSize = 0;
	DXGI_FORMAT IndexFormat = DXGI_FORMAT_R16_UINT;
	UINT IndexBufferByteSize = 0;

	// A MeshGeometry may store multiple geometries in one vertex/index buffer.
	// Use this container to define the Submesh geometries so we can draw
	// the Submeshes individually.
	std::unordered_map<std::string, SubmeshGeometry> DrawArgs;

	D3D12_VERTEX_BUFFER_VIEW VertexBufferView()const
	{
		D3D12_VERTEX_BUFFER_VIEW vbv;
		vbv.BufferLocation = VertexBufferGPU->GetGPUVirtualAddress();
		vbv.StrideInBytes = VertexByteStride;
		vbv.SizeInBytes = VertexBufferByteSize;

		return vbv;
	}

	D3D12_INDEX_BUFFER_VIEW IndexBufferView()const
	{
		D3D12_INDEX_BUFFER_VIEW ibv;
		ibv.BufferLocation = IndexBufferGPU->GetGPUVirtualAddress();
		ibv.Format = IndexFormat;
		ibv.SizeInBytes = IndexBufferByteSize;

		return ibv;
	}

	// We can free this memory after we finish upload to the GPU.
	void DisposeUploaders()
	{
		VertexBufferUploader = nullptr;
		IndexBufferUploader = nullptr;
	}
};
```