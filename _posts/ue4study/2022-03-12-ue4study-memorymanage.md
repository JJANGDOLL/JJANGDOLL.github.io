---
title: "UE4 Memory Management"

categories:
  - UE4STUDY


tags:
  - UE4
  - SmartPointer
  - UE4 MemoryManagement  

toc: true
toc_label: UE4 Memory Management
toc_sticky: true

sidebar_main: true
---

메모리 관리는 프로그래머들의 주 관심사다. 특히, 허상 포인터(Dangling pointer)는 메모리에서 이미 지워져버린 대상을 참조해서 버그를 만드는 데 충신의 역할을 한다.

UE4 에서 UObject 참조 카운팅 시스템은 UObject 에서 파생된 액터와 클래스의 메모리를 관리하는 기본적인 수단으로 UE4 프로그램 내에서 메모리가 관리된다.

만약 UObject 를 상속받지 않은 C++ 클래스를 작성한다면 `TSharedPtr/TWeakPtr` 를 활용하면 된다. 이 클래스들을 활용하면 참조가 없을 때 자동으로 오브젝트를 삭제해준다.

UE4에서 메모리 관리하는 여러가지 테크닉을 알아보자.

<br/>

# Unmanged Memory

## malloc / free

C 언어에서 메모리를 할당하는 방법은 `malloc()` 함수를 사용하는 것이다. 여전히 C++ 에서도 사용 가능하다. 이 함수는 프로그램에서 필요한 메모리를 시스템에서 할당해주는 역할을 한다. 만약 프로그램에 할당되지 않은 메모리 세그먼트에 접근을 시도하면 세그먼트 오류(Segmantation  fault) 가 발생한다.

```cpp
    int* i;

    i = (int*)malloc(sizeof(int));

    *i = 999;

    printf("i : %d", *i);

    free(i);

    i = 0;
```

<br/>

## new / delete

new 연산자는 메모리 할당 후 생성자를 호출한다는 사실을 제외하면 malloc 과 유사하다. new 를 사용한 오브젝트는 delete 연산자를 사용해 할당을 해제하자. **free()를 사용하지 말자**

```cpp
class Object
{
public:
    Object()
    {
        printf("Constructed\n");
    }

    ~Object()
    {
        printf("Destructed\n");
    }
};

Object* object = new Object();

delete object;

object = 0;
```

<br/>

## 추가 설명

메모리를 할당할 때 new, malloc 를 직접 사용하는 것은 피해야 한다. 관리되는 메모리 방식을 사용해야 사용 중인 모든 메모리가 추적되고 정리된다.

<br/>

# Managed Memory

관리되는 메모리(Managed Memory)는 서브 시스템에 의해 할당과 해제가 이루어지는 형태를 의미한다. 이 서브시스템은 프로그래머가 메모리를 할당한 후 해제하는 것을 잊지 않도록 해주어 메모리 누수가 발생하지 않도록 해준다. 

메모리를 관리하는 프로그램에서는 오브젝트를 참조하는 포인터의 수를 기억한 뒤, 참조하는 포인터가 없을 때 삭제 표시를 해두고 다음 가비지 컬렉터(GC) 에서 지워진다.

UE4 에서 메모리 관리는 형식을 지켜준다면 알아서 해준다. 엔진 내에서 사용되는 모든 오브젝트의 메모리 할당은 `NewObject<>()` 또는 `SpawnActor<>()` 를 사용해야한다.

<br/>

## NewObject / ConstructObject

Actor 상속이 아닌 UObject 상속은 `NewObject<>` Actor 또는 그 하위 일 때는 `SpawnActor<>` 를 사용한다.

```cpp
UCLASS()
class TEMP_API USample : public UObject
{
	GENERATED_BODY()
public:
	UPROPERTY()
	FString Text;

	UPROPERTY()
	FKey ShortcutKey;
};
```

이후 이 클래스를 인스턴스 하기 위해선 아래처럼 작성한다.

```cpp
USample* sample = NewObject<USample>(GetTransientPackage(), USample::StaticClass());
```

[NewObject](https://docs.unrealengine.com/4.27/en-US/API/Runtime/CoreUObject/UObject/NewObject/3/) 에서 더 자세히 확인할 수 있다.

유사한 함수로 `ConstructObject<>`가 있는데 생성 시점에 좀 더 많은 옵션을 제공한다.

<br/>

## 메모리 해제

UObject 인스턴스는 모든 참조 카운트가 사라지면 가비지 컬렉션 타겟이 된다. 또는 UObject 파생 오브젝트를 참조 카운트가 0이 되기 전에 `UObject::ConditionalBeginDestroy()` 를 사용해 수동으로 메모리에서 할 수 있다.

```cpp
USample *o = NewObject<USample>(....);
o->ConditionalBeginDestroy();
```

> 여전히 참조하고 있는 오브젝트를 해제하지 않도록 주의하자.

<br/>

## 스마트 포인터

C++ 오브젝트에 대해 delete 호출을 잊어버리는 것에 대비해 스마트 포인터를 사용한다. TSharedPtr는 커스텀 C++ 오브젝트를 참조 카운트 방식으로 생성한다. TWeakPtr 로 참조 카운트 오브젝트 이지만 삭제를 막을 수는 없다.

> UObject 와 그 파생 클래스는 TSharedPtr 를 사용할 수 없다.

<br/>

### 스마트 포인터의 종류

| Pointer Type | Use Case |
| -------------| - |
| TSharedPtr | 참조하는 오브젝트를 소유하며 참조카운트가 없을 경우 소멸시킵니다. 빈 상태일 수 있습니다. |
| TSharedRef | TSharedPtr 과 유사합니다. 다만, 항상 무언가를 참조해야하며 빈 상태일 수 없습니다. |
| TWeakPtr | 참조하는 오브젝트를 소유하지 않아 생명 주기에 영향을 주지 않습니다. 따라서, 언제든 null 이 될 수 있는 가능성이 존재합니다. |
| TUniquePtr | 참조하는 오브젝트를 유일하고 명시적으로 소유합니다. 특정 자원에 대해서 단 하나의 유니크 포인터만 있을 수 있습니다. 스코프를 벗어나면 자동으로 소멸됩니다. |

<br/>

### 스마트 포인터의 장점

| Benefit | Description | 
| --------| ----------- |
| 메모리 누수 방지 | 스마트 포인터들은 (위크 포인터 제외) 더 이상 공유된 레퍼런스가 없으면 자동으로 소멸됩니다. |
| 위크 레퍼런싱 | 위크 포인터는 참조 주기에 영향을 주지 않으면서, 삭제된 오브젝트를 참조하는 dangling pointer 를 방지합니다. |
| 선택적 스레드 안전 | 언리얼 스마트 포인터 라이브러리에는 멀티스레드에 걸쳐 참조 카운팅을 관리하는 코드인 스레드 세이프(thread-safe) 코드가 포함되어 있습니다. 스레드 안정성이 필요하지 않다면 그 대신에 향상된 퍼포먼스를 구현할 수 있습니다. |
| 런타임 안전성 | 쉐어드 레퍼런스는 절대 null 일 수 없으며 언제든지 참조 해제될 수 있습니다. |
| 명확한 의도 | 관찰자 중에서 오브젝트의 소유자를 쉽게 분별할 수 있습니다. |
| 메모리 | 스마트 포인터는 64 비트의 C++ 포인터 크기의 두 배입니다 (공유된 16 바이트의 레퍼런스 컨트롤러도 포함). 단, 예외로 유니크 포인터만 C++ 포인터의 크기와 같습니다. |

<br/>

### 예시

```cpp
class SampleClass {};
TSharedPtr<SampleClass> sharedPtr ( new SampleClass() );
```

<br/>

## TScopedPointer

TScopedPointer(범위 포인터) 스코프의 끝(블록의 끝) 에서 자동으로 삭제되는 포인터다.

```cpp
{
    TScopePointer<SampleClass> sample(this);
}
```

<br/>

# Unreal Garbage Collection

## 가비지 컬렉터와 UPROPERTY()

`TArray<>` 같은 오브젝트가 있을 때, 해당 멤버를 반드시 `UPROPERTY()` 를 선언해야 한다.

```cpp
UCLASS()
class TEMP_API USample : public UObject
{
	GENERATED_BODY()

	//TArray<FSoundEffect> Greets;
	UPROPERTY() TArray<FSoundEffect> Greets;
};
```

<br/>

## 가비지 컬렉션 강제 수행

가비지 컬렉션을 강제로 호출할 수 있습니다.

```cpp
GetWorld()->ForceGarbageCollection(true);
```