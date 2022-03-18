---
title: "UE4 Event and Delegate"

categories:
  - UE4STUDY


tags:
  - UE4
  - event
  - delegate

toc: true
toc_label: UE4 Event and Delegate
toc_sticky: true

sidebar_main: true
---

언리얼은 이벤트 방식을 사용해 효율적으로 게임 월드에서 발생한 일을 브로드 캐스트 합니다. 이벤트와 델리게이트는 클래스의 커플링을 최소화 하는 형태로 알림을 전달하는 데 매우 유용합니다.

<br/>

# 언리얼 가상함수 이벤트

언리얼은 가상 함수 형태의 이벤트 핸들러를 제공하고 이 함수를 재정의해서 커스터마이즈 할 수 있습니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "Components/BoxComponent.h"
#include "AMyEventActor.generated.h"

UCLASS()
class TEMP_API AAMyEventActor : public AActor
{
	GENERATED_BODY()
	
public:	
	AAMyEventActor()
	{
		PrimaryActorTick.bCanEverTick = true;

		TriggerBox = CreateDefaultSubobject<UBoxComponent>("TRIGGERBOX");
		TriggerBox->SetBoxExtent(FVector(200, 200, 100));
	}

	virtual void NotifyActorBeginOverlap(AActor* OtherActor) override
	{
		UE_LOG(LogTemp, Display, TEXT("BEGIN"));
	}

	virtual void NotifyActorEndOverlap(AActor* OtherActor) override
	{
		UE_LOG(LogTemp, Display, TEXT("ENd"));
	}

	UPROPERTY()
	UBoxComponent* TriggerBox;
};
```

`NotifyActorBeginOverlap` 와 `NotifyActorBeginOverlap` 는 언리얼에서 사전에 미리 정의해준 가상함수입니다. 여기서는 간단하게 사용햇지만 언리얼 메뉴얼을 참고해서 더 많은 가상함수들을 확인할 수 있습니다. `V` 아이콘이 있는 함수들이 재정의 가능한 가상함수입니다.

<br/>

# Delegate

함수 포인터는 할당한 메모리 주소를 바꿀 수 있어 매우 유용하지만 안전하지 않습니다. 델리게이트는 훨씬 안전한 함수포인터이고 어떤 함수가 연결 되어 있는지 함수 호출 순간까지도 알 필요가 없어 유연하게 함수를 호출할 수 있습니다. 

<br/>

## Binding

델리게이트를 활용하기 위해선 델리게이트 정의, 델리게이트 바인딩, 델리게이트 호출이 필요합니다.

* 델리게이트 정의

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "DelegateProvider.generated.h"

DECLARE_DELEGATE(FStandardDelegateSignature)

UCLASS()
class TEMP_API ADelegateProvider : public AActor
{
	GENERATED_BODY()
	
public:
	FStandardDelegateSignature MyStandardDelegate;
};
```

* 델리게이트 바인딩

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "DelegateProvider.h"
#include "DelegateListener.generated.h"

UCLASS()
class TEMP_API ADelegateListener : public AActor
{
	GENERATED_BODY()
	
public:	
	UPROPERTY(EditInstanceOnly)
	ADelegateProvider* Provider;

	// Sets default values for this actor's properties
	ADelegateListener()
	{
		PrimaryActorTick.bCanEverTick = true;
	}

	UFUNCTION()
	void DelegateFunc()
	{
		UE_LOG(LogTemp, Display, TEXT("DELEFUNC CALL"));
	}

protected:
	virtual void BeginPlay() override
	{
		Super::BeginPlay();

		if(Provider != nullptr)
		{
			Provider->MyStandardDelegate.BindUObject(this, &ADelegateListener::DelegateFunc);
		}
	}
};
```

* 델리게이트 호출

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "Components/BoxComponent.h"
#include "DelegateProvider.h"
#include "AMyEventActor.generated.h"


UCLASS()
class TEMP_API AAMyEventActor : public AActor
{
	GENERATED_BODY()
	
public:	
	UPROPERTY(EditInstanceOnly)
	ADelegateProvider* Provider;

	AAMyEventActor()
	{
		PrimaryActorTick.bCanEverTick = true;

		TriggerBox = CreateDefaultSubobject<UBoxComponent>("TRIGGERBOX");
		TriggerBox->SetBoxExtent(FVector(200, 200, 100));
	}


	virtual void NotifyActorBeginOverlap(AActor* OtherActor) override
	{
		if(Provider != nullptr)
		{
			Provider->MyStandardDelegate.ExecuteIfBound();
		}
	}

	UPROPERTY()
	UBoxComponent* TriggerBox;
};
```

이런식으로 작성하게 되면 커플링을 줄여 유지보수를 더 쉽게 할 수 있습니다.

<br/>

## Unbinding

델리게이트에 등록된 함수들을 해제할 수 있습니다.

단순히 해당 델리게이트 변수의 `UnBind()` 메소드를 호출하면 됩니다.

`MyStandardDelegate.Unbind();`

델리게이트를 Unbind 하여 댕글링 포인터가 되지 않도록 늘 유의합니다.

<br/>

## Delegate Parameter

델리게이트에 인자를 넘겨줄 수 있습니다. 대신 인자를 받기 위해 형태가 바뀌게 됩니다.

```cpp
//Declare

DECLARE_DELEGATE_OneParam(FParamDelegateSignature, FLinearColor)

//Binding
void DelegateFunc(FLinearColor InColor)

//Broadcast
Provider->MyParamDelegate.ExecuteIfBound(FLinearColor::Red);
```

`DelegateFunc` 에서는 `FLinearColor::Red` 값이 들어오게 됩니다.

<br/>

## Payload

델리게이트에서 Delegate 의 인자가 아닌 추가 데이터를 최대 4개 지정할 수 있습니다. 이 추가 데이터는 `DECLARE_DELEGATE_*` 이후 함수에 적용됩니다.

```cpp
//Binding
void DelegateFunc(FLinearColor InColor, bool InBool)
...
Provider->MyParamDelegate.BindUObject(this, &ADelegateListener::DelegateFunc, false);
```

## Multicast

멀티캐스트 델리게이트는 다수의 바인딩이 가능한 델리게이트 입니다. 싱글 바인딩과 비슷하지만 가장 큰 차이점은 인스턴스의 참조를 `FDelegateHandle`에 따로 저장해줘야 합니다.

액터가 파괴되면, 저장된 `FDelegateHandle` 를 해당 델리게이트에서 `Remove()` 해 줘야 바인딩 된 함수 목록에서 자기 자신을 안전하게 제거할 수 있습니다.

`ExecuteIfBound()`를 대신해 `Broadcast()` 를 사용하는데 바인딩 된 함수의 갯수와 상관 없이 안전하게 호출해준다.

* 멀티캐스트 델리게이트 정의

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "DelegateProvider.generated.h"

DECLARE_MULTICAST_DELEGATE(FMultiDelegateSignature)

UCLASS()
class TEMP_API ADelegateProvider : public AActor
{
	GENERATED_BODY()
	
public:
	FMultiDelegateSignature MyMultiDelegate;
};
```

* 멀티캐스트 델리게이트 바인딩

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "DelegateProvider.h"
#include "DelegateListener.generated.h"

UCLASS()
class TEMP_API ADelegateListener : public AActor
{
	GENERATED_BODY()
	
public:	
	UPROPERTY(EditInstanceOnly)
	ADelegateProvider* Provider;

	// Sets default values for this actor's properties
	ADelegateListener()
	{
		PrimaryActorTick.bCanEverTick = true;
	}

	UFUNCTION()
	void DelegateFunc()
	{
		UE_LOG(LogTemp, Display, TEXT("DELEFUNC CALL"));
	}

	UFUNCTION()
	void DelegateFunc2()
	{
		UE_LOG(LogTemp, Display, TEXT("DELEFUNC2 CALL"));
	}

	FDelegateHandle DelegateHandle;
	FDelegateHandle DelegateHandle2;

protected:
	virtual void BeginPlay() override
	{
		Super::BeginPlay();

		if(Provider != nullptr)
		{
			DelegateHandle = Provider->MyMultiDelegate.AddUObject(this, &ADelegateListener::DelegateFunc);
			DelegateHandle2 = Provider->MyMultiDelegate.AddUObject(this, &ADelegateListener::DelegateFunc2);
		}
	}

	virtual void EndPlay(const EEndPlayReason::Type EndPlayReason) override
	{
		Super::EndPlay(EndPlayReason);

		Provider->MyMultiDelegate.Remove(DelegateHandle);
		Provider->MyMultiDelegate.Remove(DelegateHandle2);
	}
};

```

* 멀티캐스트 델리게이트 호출

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "Components/BoxComponent.h"
#include "DelegateProvider.h"
#include "AMyEventActor.generated.h"


UCLASS()
class TEMP_API AAMyEventActor : public AActor
{
	GENERATED_BODY()
	
public:	
	UPROPERTY(EditInstanceOnly)
	ADelegateProvider* Provider;

	AAMyEventActor()
	{
		PrimaryActorTick.bCanEverTick = true;

		TriggerBox = CreateDefaultSubobject<UBoxComponent>("TRIGGERBOX");
		TriggerBox->SetBoxExtent(FVector(200, 200, 100));
	}


	virtual void NotifyActorBeginOverlap(AActor* OtherActor) override
	{
		if(Provider != nullptr)
		{
			Provider->MyMultiDelegate.Broadcast();
		}
	}

	UPROPERTY()
	UBoxComponent* TriggerBox;
};
```

<br/>