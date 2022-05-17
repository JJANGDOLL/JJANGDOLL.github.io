---
title: "D3D12 CPU와 GPU의 상호작용"

categories:
  - D3D12


tags:
  - d3d12
  - cpu gpu interact

toc: true
toc_label: CPU와 GPU의 상호작용
toc_sticky: true

sidebar_main: true
---

그래픽 프로그래밍은 CPU, GPU 두개의 프로세서가 작동한다는 점을 이해할 필요가 있다. 서로 따로따로 병려적으로 동작하지만 최대한의 성능을 위해선 둘 다 바쁘게 돌아가도록 해야하고 동기화를 최소화 해야한다. 동기화는 한 장치가 다른 장치가 마칠 때 까지 기다리는 것을 의미한다. 즉, 동기화는 병렬성을 망친다.

<br/>

# Command Queue, Command List

GPU에는 Command Queue가 하나 있다. CPU는 D3DAPI를 통해 Command List 에 명령을 전달한다. 중요한 점은 큐에 전달했다 해도 GPU에서 바로 실행하는 것이 아닌, GPU가 처리할 준비가 되어야 시작한다. 

명령 대기열이 비면 GPU는 그냥 논다. 반대로 대기열이 꽉 차면 GPU가 명령을 처리해서 Queue에 자리가 생길 때 까지 CPU가 논다. 우리는 CPU와 GPU 모두 놀지 않게 해야한다.

![cg_q](/assets/images/d3d12/cg_q.png)

D3D에서 대기열을 대표하는 인터페이스는 ID3D12CommandQueue 이다. D3D12_COMMAND_QUEUE_DESC 구조체를 채운 뒤 호출해야 한다.

```cpp
Microsoft::WRL::ComPtr<ID3D12CommandQueue> mCommandQueue;
D3D12_COMMAND_QUEUE_DESC queueDesc = {};
queueDesc.Type = D3D12_COMMAND_LIST_TYPE_DIRECT;
queueDesc.Flags = D3D12_COMMAND_QUEUE_FLAG_NONE;
ThrowIfFailed(md3dDevice->CreateCommandQueue(
 &queueDesc, IID_PPV_ARGS(&mCommandQueue)));
```

IID_PPV_ARGS 매크로는 아래와 같다.

```cpp
#define IID_PPV_ARGS(ppType) __uuidof(**(ppType)), IID_PPV_ARGS_Helper(ppType)
```

`__uuidof(**(pptype))` 는 `(**(ppType))` 은 COM 인터페이스 ID로 평가되는데 그 ID는 ID3D12CommandQueue 이다. D3D12 API 에는 생성하고자 하는 인터페이스의 COM ID 에 따라 void ** 를 받는 함수가 많아서 이 매크로들이 책 전반에 쓰인다. 이 인터페이스의 메소드 중 하나는 명령어를 대기열에 추가하는 ExecuteCommandLists 이다.

```cpp
void ID3D12CommandQueue::ExecuteCommandLists(
 // Number of commands lists in the array
 UINT Count,
 // Pointer to the first element in an array of command lists
 ID3D12CommandList *const *ppCommandLists); 
```

명령 목표를 대표하는 인터페이스는 ID3D12CommandList 지만 실제 그래픽 작업을 위한 인터페이스는 ID3D12GraphicsCommandList 라는 인터페이스로 대표된다. 아래 코드는 뷰포트를 설정하고 렌더 타겟 뷰를 지우고 드로우를 호출하는 명령을 호출한다.

```cpp
// mCommandList pointer to ID3D12CommandList
mCommandList->RSSetViewports(1, &mScreenViewport);
mCommandList->ClearRenderTargetView(mBackBufferView,
 Colors::LightSteelBlue, 0, nullptr);
mCommandList->DrawIndexedInstanced(36, 1, 0, 0, 0);
```

이 메소드들은 즉시 실행될 것 같지만 그렇지 않고 Command list에 추가만한다. 이후 ExecuteCOmmandLists 를 호출해야 CommandQueue에 추가되며 GPU가 명령들을 뽑아서 실행하기 시작한다.

명령 추가가 끝났으면 ID3D12GraphicsCommandList::Close 를 호출해 명령들의 기록이 끝났음을 D3D에게 알려줘야 한다.

```cpp
// Done recording commands.
mCommandList->Close();
```

Command List 에는 ID3D12CommandAllocator 형식의 메모리 할당자가 연관된다. ID3D12Device 를 이용한다.

```cpp
HRESULT ID3D12Device::CreateCommandAllocator(
 D3D12_COMMAND_LIST_TYPE type,
 REFIID riid,
 void **ppCommandAllocator);
```

Command List 역시 ID3D12Device 로 생성한다.

```cpp
HRESULT ID3D12Device::CreateCommandList(
 UINT nodeMask,
 D3D12_COMMAND_LIST_TYPE type,
 ID3D12CommandAllocator *pCommandAllocator,
 ID3D12PipelineState *pInitialState,
 REFIID riid,
 void **ppCommandList);
```

한 할당자를 여러 Command List 에 연관시켜도 되지만, 명령들을 여러 Command List 에 동시에 기록할 수는 없다. 즉, 현재 명령을 추가하는 명령 목록을 제외한 모든 명령 목록은 단혀 있어야 한다. 같은 할당자로 두 명령 목록을 연달아 생성하면 아래 오류가 발생한다.

```text
D3D12 ERROR: ID3D12CommandList::{Create,Reset}CommandList: The command
allocator is currently in-use by another command list.
```

ID3D12CommandQueue::ExecuteCommandList(C) 호출 이후 ID3D12CommandList::Rset 을 호출하면 C의 내부 메모리를 새로운 명령 들을 기록하는 데 재사용 할 수 있다.

```cpp
HRESULT ID3D12CommandList::Reset(
 ID3D12CommandAllocator *pAllocator,
 ID3D12PipelineState *pInitialState);
```

이 메소드는 주어진 CommandList 를 처음 생성했을 때와 같은 상태로 만든다.

하나의 프레임을 완성하는데 필요한 렌더링 명령들을 모두 GPU에서 제출한 후, Command Allocator 메모리를 다음 프레임을 위해 재사용 해야 한다. 이때는 ID3D12CommandAllocator::Reset 을 사용한다.

```cpp
HRESULT ID3D12CommandAllocator::Reset(void);
```

**GPU가 모든 Command 를 실행했음이 확실해 지기 전까진 Command Allocator 를 재설정 하지 말아야 한다.**

<br/>

# CPU/GPU 동기화

CPU가 R이라는 위치에 자원을 담는다고 하자. CPU는 위치 $p_1$ 를 R에 담고 R을 참조하는 그리기 명령 C를 Command List 에 추가한다. 추가하는 메소드는 CPU의 실행을 차단하지 않기에 다음 작업으로 넘어가는데 만약 GPU가 C를 실행하기 전에 CPU가 R의 리소스를 덮어 쓴다면 의도대로 동작하지 않게 된다.

이런 문제를 해결하기 위한 방법중 하나는 GPU가 특정 명령까지의 모든 명령을 처리하기 전까지 CPU가 대기하게 한다. 명령을 처리하는 것을 가르키 flush 라고 말한다. 이때 필요한 것이 fence 라고 부르는 객체이며 ID3D12Fence 인터페이스를 사용한다.

```cpp
HRESULT ID3D12Device::CreateFence(
 UINT64 InitialValue,
 D3D12_FENCE_FLAGS Flags,
 REFIID riid,
 void **ppFence);

 // Example
ThrowIfFailed(md3dDevice->CreateFence(
 0,
 D3D12_FENCE_FLAG_NONE,
 IID_PPV_ARGS(&mFence)));
```

울타리 객체는 UINT64 값 하나를 관리한다. 이 값은 그냥 시간상의 특정 울타리 지점을 식별하는 정수이다. 이 책의 예제들은 처음에는 이 값을 0으로 두고 새 울타리 지점을 만들 때 마다 1씩 증가시킨다. 

울타리를 이용해서 명령 대기열을 비우는 방법을 보여주는 코드이다.

```cpp
UINT64 mCurrentFence = 0;
void D3DApp::FlushCommandQueue()
{
  // Advance the fence value to mark commands up to this fence point.
  mCurrentFence++;
  // Add an instruction to the command queue to set a new fence point.
  // Because we are on the GPU timeline, the new fence point won’t be
  // set until the GPU finishes processing all the commands prior to
  // this Signal().
  ThrowIfFailed(mCommandQueue->Signal(mFence.Get(), mCurrentFence));
  // Wait until the GPU has completed commands up to this fence point.
  if(mFence->GetCompletedValue() < mCurrentFence)
  {
    HANDLE eventHandle = CreateEventEx(nullptr, false, false, EVENT_ALL_ACCESS);
    // Fire event when GPU hits current fence.
    ThrowIfFailed(mFence->SetEventOnCompletion(mCurrentFence, eventHandle));
    // Wait until the GPU hits current fence event is fired.
    WaitForSingleObject(eventHandle, INFINITE);
    CloseHandle(eventHandle);
  }
}
```

![cg_s](/assets/images/d3d12/cg_s.png)

이렇게 되면 CPU가 C를 제출한 후 $p_2$가 R의 리소스를 덮어 쓰기 전에 먼저 명령 대기열을 실행하게된다. GPU가 실행 하기 전까지 CPU는 대기하기에 이상적인 해결책은 아니지만 간단한 해결책이다.

<br/>

# resource transitions

흔히 쓰이는 렌더링 효과 중에 GPU가 R에 기록하고 이후 R의 자료를 읽는 방식이 많다. 하지만 다 쓰지도 않았는데 읽거나 기록을 하지도 않았는데 읽으면 문제가 생긴다. 이를 resource hazzard 라고 부른다. 따라서 D3D는 자원들에 상태를 부여한다.

새로운 생성된 자원은 default state 로 시작하고, D3D로의 상태의 변경에 대한 보고는 전적으로 응용 프로그램의 몫이다. 덕분에 GPU는 상태를 변경하거나 자원의 위험한 상황을 방지하는데 필요한 일을 진행할 수 있다.

자원 상태 전이는 transition resource harrier 의 배열을 설정해서 지정한다. 배열이라 API 호출 한번으로 여러 개의 자원의 상태를 설정할 수 있다. D3D12_RESOURCE_BARRIER_DESC 구조체 디스크립터가 사용된다.

```cpp
struct CD3DX12_RESOURCE_BARRIER : public D3D12_RESOURCE_BARRIER
{
 // [...] convenience methods
  static inline CD3DX12_RESOURCE_BARRIER Transition(
  _In_ ID3D12Resource* pResource,
  D3D12_RESOURCE_STATES stateBefore,
  D3D12_RESOURCE_STATES stateAfter,
  UINT subresource = D3D12_RESOURCE_BARRIER_ALL_SUBRESOURCES,
    D3D12_RESOURCE_BARRIER_FLAGS flags = D3D12_RESOURCE_BARRIER_FLAG_NONE)
  {
      CD3DX12_RESOURCE_BARRIER result;
      ZeroMemory(&result, sizeof(result));
      D3D12_RESOURCE_BARRIER &barrier = result;
      result.Type = D3D12_RESOURCE_BARRIER_TYPE_TRANSITION;
      result.Flags = flags;
      barrier.Transition.pResource = pResource;
      barrier.Transition.StateBefore = stateBefore;
      barrier.Transition.StateAfter = stateAfter;
      barrier.Transition.Subresource = subresource;
      return result;
  }
 // 
}
```

D3D12 의 구조체들 대부분에는 '확장 버전'이 존재하며 `CD3DX12_`로 시작하는 확장 버전들은 d3dx12.h에 정의되어 있다.

```cpp
mCommandList->ResourceBarrier(1,
 &CD3DX12_RESOURCE_BARRIER::Transition(
 CurrentBackBuffer(),
 D3D12_RESOURCE_STATE_PRESENT,
 D3D12_RESOURCE_STATE_RENDER_TARGET));
```

화면에 표시할 이미지를 나타내는 텍스쳐 자원의 presentation state 를 render target state로 변경했다. GPU에게 자원의 상태가 변경됨을 알려주는 하나의 명령이라고 생각하면 된다. 덕분에 GPU는 이후 명령들을 실행할 때 자원 위험 상황을 피하는 절차를 밟을 수 있다.

<br/>

# multisampling with commands

D3D12 는 다중 스레드를 효율적으로 활용할 수 있도록 설계되엇다. command list 의 설계는 D3D가 다중 스레드 적용의 장점을 취하는 방법이다. 물체가 많은 큰 장면을 그릴 때, 장면 전체를 하나의 command list 로 그리면 cpu 시간이 오래걸린다. ㅇ따라서 여러개의 command list 를 병렬로 구축하는 것이다.

command list 다수를 구축할 때 멀티 스레드를 사용한다면 주의해야 할 사항이다.

* command list 는 free-threading 이 아니다. 스레드는 각자 자신만의 command list 를 가진다.
* command allocator 도 free-threading 이 아니다. 일반적으로 각 스레드는 각자 자신만의 command allocator 를 가진다.
* command queue 는 freethreading 이다. 즉, 여러 스레드가 같은 command queue에 접근해 메소드들을 동시에 호출 가능하다.
* 성능 상의 이유로, 응용 프로그램은 동시에 기록할 수 있는 command list 최대 갯수를 반드시 초기화 시점에서 설정해야 한다.

이 책에서는 멀티 스레드를 사용하지 않는다. DirectX SDK Multithreading12 예제를 보고 공부해보길 바란다.

<br/>

