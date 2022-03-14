---
title: "UE4 Actor and Component"

categories:
  - UE4STUDY


tags:
  - UE4
  - Actor
  - Component

toc: true
toc_label: UE4 Actor and Component
toc_sticky: true

sidebar_main: true
---

액터는 월드에 배치하기 위해선 반드시 상속받아야 하는 클래스입니다. 이 액터에 다양한 컴포넌트를 부착함으로써 특성을 부여할 수 있습니다.

# Actor

## Create actor

UE4 에서 액터를 생성하는 방법은 매우 쉽습니다.

**반드시 경로를 주의합니다. [ProjectName/source/] 아래의 경로에 저장되어야 합니다.**

![CreateActor1](/assets/images/ue4/createactor_1.png)

![CreateActor2](/assets/images/ue4/createactor_2.png)

![CreateActor2](/assets/images/ue4/createactor_3.png)

기다리면 Visual Studio 가 열리며 헤더 파일와 cpp 파일이 생성됩니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "MyActor.generated.h"

UCLASS()
class TEMP_API AMyActor : public AActor
{
	GENERATED_BODY()
	
public:	
	// Sets default values for this actor's properties
	AMyActor();

protected:
	// Called when the game starts or when spawned
	virtual void BeginPlay() override;

public:	
	// Called every frame
	virtual void Tick(float DeltaTime) override;

};
```

각 줄의 의미는 아래와 같습니다.

* #pragma : 전치리 명령문 입니다. include 를 여러번 참조하면서 발생하는 문제를 예방하기 위해 사용합니다. C++ 11 에서 채택되었습니다.
* #include "CoreMinimal.h" : FString, TArray, Vector 등 자주 사용되는 다수의 클래스 정의를 포함하는 헤더파일입니다. 이 파일이 없어도 빌드는 가능하지만 생성된 스크립트 파일에 기본적으로 포함됩니다.
* #include "GameFramework/Actor.h" : Actor 를 상속받으므로 당연히 상속받는 헤더파일입니다.
* #include "MyActor.generated.h" : 모든 Actor 클래스는 자신의 generated.h 파일을 반드시 포함해야 합니다. 이 generated.h 파일은 이 소스코드에서 감지한 매크로를 기반으로 언리얼 헤더 툴(UHT)에 의해 자동으로 생성됩니다.
* UCLASS() : UCLASS 는 클래스가 언리얼의 리플렉션 시스템에 노출될 것임을 나타낼 수 있는 매크로입니다. 리플렉션 시스템을 활용하면 런타임 동안 오브젝트 속성을 검사하거나 순회할 수 있으며 GC를 위해 오브젝트에 대한 참조를 관리할 수 있습니다.
* class TEMP_API AMyActor : 실제로 우리가 생성한 클래스의 선언부입니다. TEMP_API 는 UHT 가 생성했으며, 프로젝트 모듈의 클래스가 DLL에 제대로 내보내지도록 함으로써 윈도우 환경에서 프로젝트가 정상적으로 컴파일 되도록 도와줍니다. AMyActor 와 AActor 모두 A의 접두사가 붙어있는데 언리얼이 Actor 에서 상속된 네이티브 클래스에 부여한 네이밍 규칙이다.
* GENERATED_BODY() : UE 타입 시스템에 필요한, 자동으로 생성된 기능을 포함하도록 하는 UHT 매크로입니다.

```cpp
#include "MyActor.h"

// Sets default values
AMyActor::AMyActor()
{
 	// Set this actor to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
	PrimaryActorTick.bCanEverTick = true;

}

// Called when the game starts or when spawned
void AMyActor::BeginPlay()
{
	Super::BeginPlay();
	
}

// Called every frame
void AMyActor::Tick(float DeltaTime)
{
	Super::Tick(DeltaTime);

}
```

* PrimaryActorTick.bCanEverTick = true; : Actor의 Tick 을 활성화합니다.

<br/>

## Instance Actor

액터를 인스턴스 하기 위해선 SpawnActor 와 같은 함수를 사용해주면 됩니다.

```cpp
FTransform spawnLoc;
GetWorld()->SpawnActor<AMyActor>(AMyActor::StaticClass(), spawnLoc);
```

<br/>

## Serialize Actor to BP

생성한 액터를 블루프린트에 노출하고, 구현한 기능을 호출해봅시다.

멤버나 함수를 BP에서 직렬화하기 위해선 알맞은 UE 매크로를 사용해야 합니다.

```cpp
UCLASS()
class TEMP_API AMyActor : public AActor
{
	GENERATED_BODY()
	
public:	
	// Sets default values for this actor's properties
	AMyActor();

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category="Prop")
	FString Name;

	UFUNCTION(BlueprintCallable, Category = "Prop")
	FString GetName()
	{
		return FString::Printf(TEXT("Name : %s"), *Name);
	};
};
```

이제 이 클래스를 상속받는 BP 를 생성해봅시다.

![SerializeActor1](/assets/images/ue4/serializeactor_1.png)

![SerializeActor2](/assets/images/ue4/serializeactor_2.png)

![SerializeActor3](/assets/images/ue4/serializeactor_3.png)

이후 BP의 디폴트 창에서 카테고리로 검색해보면 CPP에서 생성한 멤버를 확인할 수 있습니다.

![SerializeActor4](/assets/images/ue4/serializeactor_4.png)

이제 C++ 로 생성한 클래스를 BP에서도 상속받아 사용할 수 있습니다!

<br/>

## Destroy Actor

액터를 소멸하는 방법은 간단합니다. `Destroy()` 메소드를 호출해주면 됩니다.

```cpp
[ActorInstance]->Destroy();
```

<br/>

### 액터 지연 삭제

액터를 일정시간 동안 유지 후에 삭제시키는 방법도 존재합니다. `SetLifeSpan()` 함수를 사용해서 일정 시간 이후 자동으로 소멸되도록 할 수 있습니다.

```cpp
[ActorInstance]->SetLifeSpan(10);
```

이 액터는 10초후 파괴됩니다.

<br/>

# Component

액터를 다양한 기능을 담을 수 있는 바구니라고 한다면, 컴포넌트는 실제로 담기는 재료들로 액터의 기능와 특성을 부여해줍니다.

<br/>

## Component Initialize

실제로 AMyActor 를 월드에 배치하면 Transform 이 없어서 좌표를 지정할 수 없는 것을 알 수 있습니다. USceneComponent 를 상속받은 컴포넌트를 추가함으로써 좌표를 설정할 수 있습니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "Components/SceneComponent.h"
#include "MyActor.generated.h"

UCLASS()
class TEMP_API AMyActor : public AActor
{
	GENERATED_BODY()
	
public:	
    AMyActor()
    {
        Scene = CreateDefaultSubobject<USceneComponent>("Transform");
    }

	UPROPERTY()
	USceneComponent* Scene;
};
```

`UPROPERTY()` 매크로를 사용하면 이 멤버는 참조된 것으로 간주되어 GC 에서 제외됩니다.

이 코드는 생성자에서 템플릿 함수 `CreateDefaultSubobject`를 사용해 컴포넌트를 적절히 초기화하고 값을 할당합니다. 

이제 월드에 배치하면 Transform 을 지정할 수 있게됩니다.

<br/>

## FObjectFinder

지정한 에셋을 불러오고 초기화 할 수 있습니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "Components/StaticMeshComponent.h"
#include "MyActor.generated.h"

UCLASS()
class TEMP_API AMyActor : public AActor
{
	GENERATED_BODY()
	
public:	
	// Sets default values for this actor's properties

    AMyActor()
    {
		Mesh = CreateDefaultSubobject<UStaticMeshComponent>("MESH");

		auto MeshAsset = ConstructorHelpers::FObjectFinder<UStaticMesh>(TEXT("StaticMesh'/Engine/BasicShapes/Cube.Cube'"));
		if(MeshAsset.Succeeded())
		{
			Mesh->SetStaticMesh(MeshAsset.Object);
		}
    }

	UPROPERTY()
	UStaticMeshComponent* Mesh;
};
```

`FObjectFinder` 메소드는 에셋 로딩을 도와주는 클래스 템플릿입니다. 인자에는 에셋의 클래스 이름과 경로를 가진 문자열을 인자로 받습니다. 문자열은 `{ObjectType}/Path/To/Asset.Asset` 형태를 가집니다. 컨텐츠 브라우저에서 `Copy Reference`를 하면 더 쉽게 가져올 수 있습니다.

![CopyReference](/assets/images/ue4/copyreference.png)

<br/>

## Component Layer

USceneComponent 를 상속받은 컴포넌트 끼리는 서로 계층 구조를 가질 수 있습니다. 부모 자식 간으로 연결되어 있을 때 자식 컴포넌트는 부모 컴포넌트의 변경에 영향을 받게 됩니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "Components/StaticMeshComponent.h"
#include "Components/SceneComponent.h"
#include "MyActor.generated.h"

UCLASS()
class TEMP_API AMyActor : public AActor
{
	GENERATED_BODY()
	
public:	
	// Sets default values for this actor's properties

    AMyActor()
    {
		Root = CreateDefaultSubobject<USceneComponent>("ROOT");
		RootComponent = Root;

		Mesh = CreateDefaultSubobject<UStaticMeshComponent>("MESH");

		auto MeshAsset = ConstructorHelpers::FObjectFinder<UStaticMesh>(TEXT("StaticMesh'/Engine/BasicShapes/Cube.Cube'"));
		if(MeshAsset.Succeeded())
		{
			Mesh->SetStaticMesh(MeshAsset.Object);
		}

		Mesh->AttachTo(Root);
    }

	UPROPERTY(VisibleInstanceOnly)
	USceneComponent* Root;

	UPROPERTY(VisibleInstanceOnly)
	UStaticMeshComponent* Mesh;
};
```

`AttachTo` 함수를 이용해 컴포넌트간 계층을 설정할 수 있습니다.

![ComponentLayer](/assets/images/ue4/componentlayer.png)

<br/>

## Custom ActorComponent

ActorComponent는 액터 간 공유해야 하는 기능을 쉽게 구현해서 액터에 붙일 수 있는 방법입니다. 액터 컴포넌트는 렌더링되지는 않지만, 이벤트를 구독하건가 내부 액터의 다른 컴포넌트와 통신하는 등 작업을 수행할 수 있습니다.

커스터마이즈 가능한 ActorComponent 를 만들어봅시다.

![CustomActorComponent](/assets/images/ue4/customactorcomponent.png)

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Components/ActorComponent.h"
#include "MyActorComponent.generated.h"


UCLASS( ClassGroup=(Custom), meta=(BlueprintSpawnableComponent) )
class TEMP_API UMyActorComponent : public UActorComponent
{
	GENERATED_BODY()

public:	
	// Sets default values for this component's properties
	UMyActorComponent()
	{
		PrimaryComponentTick.bCanEverTick = true;

		MovementRadius = 5.0f;
	}

	virtual void TickComponent(float DeltaTime, enum ELevelTick TickType, FActorComponentTickFunction* ThisTickFunction) override
	{
		Super::TickComponent(DeltaTime, TickType, ThisTickFunction);

		AActor* Parent = GetOwner();

		if(Parent)
		{
			auto NewPos = Parent->GetActorLocation() + FVector(FMath::FRandRange(-1, 1) * MovementRadius, FMath::FRandRange(-1, 1) * MovementRadius, FMath::FRandRange(-1, 1) * MovementRadius);
			Parent->SetActorLocation(NewPos);
		}
	}

	UPROPERTY()
	float MovementRadius;
};
```

이후 액터 컴포넌트를 액터에 추가하고 플레이 하면 액터가 미친듯이 움직이기 시작한다!! (추가 방법은 위에 참조)

<br/>

## Custom SceneCompoennt

씬 컴포넌트는 액터 컴포넌트의 서브클래스이면서 위치, 회전, 스케일 의 Transform 값을 가지고 있습니다. 생성 방법은 Custom ActorComponent 와 크게 다르지 않습니다.