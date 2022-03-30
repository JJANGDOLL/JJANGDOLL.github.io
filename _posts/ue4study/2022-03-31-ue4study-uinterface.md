---
title: "UE4 UInterface"

categories:
  - UE4STUDY


tags:
  - UE4
  - uinterface

toc: true
toc_label: UE4 UInterface
toc_sticky: true

sidebar_main: true
---

UInterface 를 활용해서 클래스 커플링을 최소화하고 코드를 깨끗하게 유지합니다.

게임 프로젝트에서 공통 기능을 사용하기 위해 서로 다른 오브젝트가 필요할 수 있지만, 이 오브젝트끼리는 관계가 없으므로 상속을 사용하는 것은 적절하지 않다. CPP는 이 문제를 해결하기 위해 다중 상속을 사용한다.

하지만, 만약 언리얼에서 다중 상속을 받아 블루프린트에서 접근할 수 있게 하려면 두 부모 클래스 모두를 UCLASS 로 만들어야 하는데 여기에는 문제가 있습니다. 한 오브젝트에서 UCLASS를 두번 상속하면 UObject 의 깔끔한 순회가 망가집니다. 그렇기에 언리얼 코드베이스는 C#에서 명시적 인터페이스 타입의 개녑을 빌려 문제를 해결합니다.

구성(Composition) 대신 이 접근방식을 사용하는 이유는 일반적으로 컴포넌트는 UObject가 아닌 액터에서만 사용할 수 있습니다. 하지만 인터페이스는 모든 UObject에 적용할 수 있습니다. 이 말은, 더 이상 오브젝트와 컴포넌트 사이에 `is~a` 관계가 아닌 `has~a` 관계만 모델링 할 수 있습니다.

<br/>

# UInterface 생성

![UInterface_Create](/assets/images/ue4/uinterface_create.png)

```cpp
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "UObject/Interface.h"
#include "MyInterface.generated.h"

// This class does not need to be modified.
UINTERFACE(MinimalAPI)
class UMyInterface : public UInterface
{
	GENERATED_BODY()
};

/**
 * 
 */
class TEMP_API IMyInterface
{
	GENERATED_BODY()

	// Add interface functions to this class. This is the class that will be inherited to implement this interface.
public:
	virtual FString GetTestName()
	{
		unimplemented();
		return FString();
	}
};
```

적절한 이름`MyInterface` 를 넣으면 알아서 생성되고 비쥬얼 스튜디오가 열리게 됩니다. 여기서 주의해야 할 부분은 순수 가상함수로만 이루어진 인터페이스와 달리 구현이 가능하다는 것이다. 구현이나 순수가상함수 지정 없이 빌드를 하게되면 에러가 발생합니다.

`umimplemented()` 매크로는 호출되면 assert 의 역할을 담당합니다.

<br/>

# UInterface 상속

새로운 Actor를 만들고 `MyInterface` 클래스를 상속받아 구현합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "MyInterface.h"
#include "SingleInterfaceActor.generated.h"

UCLASS()
class TEMP_API ASingleInterfaceActor : public AActor, public IMyInterface
{
	GENERATED_BODY()
	
public:	
    ASingleInterfaceActor() {};

public:
    virtual FString GetTestName() override
    {
        //return IMyInterface::GetTestName();
        return FString(TEXT("override"));
    }
};
```

Super 클래스는 UClass 를 참조하므로 override 안에서 Super 을 하기 위해선 `Super` 대신 `IIterface::[specifier]` 를 사용합니다. 

<br/>

# UInterface 구현 확인

C++ 코드에서 오브젝트가 특정 UInterface를 구현했는지 검사합니다.

```cpp
ASingleInterfaceActor* actor;
UClass* actorClass = actor->GetClass();
if(actorClass->ImplementsInterface(UMyInterface::StaticClass()))
{
	// implements interface!!"
}
```

<br/>

# UInterface 캐스팅

UInterface는 Cast를 통해 공통 인터페이스를 구현하는 서로 다른 오브젝트 컬렉션을 처리할 수 있습니다.

> 클래스가 블루프린트를 통해 인터페이스를 구현하는 경우 동작하지 않습니다.

```cpp
ASingleInterfaceActor* actor;
IMyInterface* myInterface = Cast<IMyInterface>(actor);
myInterface->GetTestName();
```

<br/>

# UInterface를 상속받는 UInterface

UInterface 를 상속받아 더 큰 UInterface를 만들어야 할 수도 있습니다.

우선 부모 인터페이스를 정의합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/Interface.h"
#include "Killable.generated.h"

UINTERFACE(meta = (CannotImplementInterfaceInBlueprint))
class UKillable : public UInterface
{
	GENERATED_BODY()
};

class TEMP_API IKillable
{
	GENERATED_BODY()

public:
	virtual bool IsDead()
	{
		return false;
	}

	virtual void Die()
	{
		AActor* me = Cast<AActor>(this);

		if(me)
		{
			me->Destroy();
		}
	}
};
```

`IKillable` 을 상속받는 `IUndead` 클래스를 만듭니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/Interface.h"
#include "Killable.h"
#include "Undead.generated.h"

UINTERFACE(MinimalAPI)
class UUndead : public UKillable
{
	GENERATED_BODY()
};

class TEMP_API IUndead : public IKillable
{
	GENERATED_BODY()

public:
    virtual void Die() override
    {
        Turn();
    }

    virtual bool IsDead() override
    {
        return true;
    }

    void Turn()
    {
        // Turn to undead
    }
};
```

이후 IUndead 나 IKillable 인터페이스를 활용하면 됩니다.

<br/>

# BP에 UInterface 메소드 노출

CPP로 작성한 인터페이스 메소드를 BP에서 노출되도록 합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/Interface.h"
#include "PostBeginPlay.generated.h"

// This class does not need to be modified.
UINTERFACE(meta = (CannotImplementInterfaceInBlueprint))
class UPostBeginPlay : public UInterface
{
	GENERATED_BODY()
};

class TEMP_API IPostBeginPlay
{
	GENERATED_BODY()

	// Add interface functions to this class. This is the class that will be inherited to implement this interface.
public:
	UFUNCTION(BlueprintCallable, Category = Test)
	virtual void OnPostBeginPlay()
	{
		// PostBeginPlay!
	}
};
```

이후 이 인터페이스를 상속받는 액터를 하나 생성하고 블루프린트에서 메소드 목록을 살펴보자.

![UInterface_toBP](/assets/images/ue4/uinterface_tobp.png)

<br/>

# BP에서 UInterface 구현

UInterface의 메소드를 BP에서 구현하도록 위임할 수 있습니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/Interface.h"
#include "AttackAvoider.generated.h"

UINTERFACE(MinimalAPI)
class UAttackAvoider : public UInterface
{
	GENERATED_BODY()
};

class TEMP_API IAttackAvoider
{
	GENERATED_BODY()
public:
	UFUNCTION(BlueprintImplementableEvent, BlueprintCallable, Category=AttackAvoider)
	void AttackIncoming(AActor* AttackActor);
};
```

이 경우엔 AttackIncoming 메소드는 구현이 없어야 빌드가 성공합니다.

새로운 BP를 만들어서 이 인터페이스를 상속받도록 합니다.

![UInterface_cs](/assets/images/ue4/uinterface_classsetting.png)

![UInterface_bpif](/assets/images/ue4/uinterface_bpinterface.png)

![UInterface_dele](/assets/images/ue4/uinterface_delegate.png)

<br/>

# BP에서 UInterface 재정의

UInterface 함수를 CPP에서도 구현하지만 BP에서도 재정의 할 수 있습니다.

우선 기본이 되는 인터페이스를 작성합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/Interface.h"
#include "Wearable.generated.h"

UINTERFACE(MinimalAPI)
class UWearable : public UInterface
{
	GENERATED_BODY()
};

class TEMP_API IWearable
{
	GENERATED_BODY()

public:
	UFUNCTION(BlueprintNativeEvent, BlueprintCallable, Category = Wearable)
	int32 GetStrenthRequirement();

	UFUNCTION(BlueprintNativeEvent, BlueprintCallable, Category = Wearable)
	bool CanEquip(APawn* wearer);

	UFUNCTION(BlueprintNativeEvent, BlueprintCallable, Category = Wearable)
	void OnEquip(APawn* wearer);
};
```

새로운 CPP 기반 클래스를 생성하고 인터페이스를 구현합니다. 여기서 주의해야 할 점은 함수명에 `_Implementation` 접미사를 붙여야 한다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "Wearable.h"
#include "Boots.generated.h"

UCLASS()
class TEMP_API ABoots : public AActor, public IWearable
{
	GENERATED_BODY()
public:

    virtual bool CanEquip_Implementation(APawn* wearer) override
    {
        return true;
    }

    virtual int32 GetStrenthRequirement_Implementation() override
    {
        return 100;
    }

    virtual void OnEquip_Implementation(APawn* wearer) override
    {
        // EQUIP!!
    }
};
```

이번엔 새 BP를 만들고 인터페이스를 설정한 뒤, 함수 중 하나를 오버라이딩 합니다.

![UInterface_override](/assets/images/ue4/uinterface_override.png)

<br/>

# CPP에서 BP로 정의한 인터페이스 호출

CPP에서 BP에서 정의한 인터페이스를 호출할 수 있습니다.

`IWearable::Execute_GetStrenthRequirement([target pointer], [args...]);`

<br/>
