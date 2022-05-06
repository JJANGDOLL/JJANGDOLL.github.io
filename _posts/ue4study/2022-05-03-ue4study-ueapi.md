---
title: "UE4 API"

categories:
  - UE4STUDY


tags:
  - UE4
  - UE API


toc: true
toc_label: UE4 API
toc_sticky: true

sidebar_main: true
---

언리얼에서 제공하는 API를 살펴봅니다.

<br/>

# FMessageLog

FMessageLog 는 MessageLog(Window::Developer Tools::Message Log)와 OutputLog(Window::Developer Tools::Output Log) 동시에 작성할 때 사용하는 오브젝트입니다.

`[projectname].h` 와 `[projectname].cpp` 에 아래를 정의합니다.

```cpp
[projectname].h
#define LOCTEXT_NAMESPACE "TEMP_LOC_NS"
#define FTEXT(x) LOCTEXT(x, x)

extern FName LoggerName;

void CreateLog(FName logName);


[projectname].cpp

#include "MessageLog/Public/MessageLogModule.h"
#include "Logging/MessageLog.h"

IMPLEMENT_PRIMARY_GAME_MODULE(FDefaultGameModuleImpl, temp, "temp");


FName LoggerName("MessageLoggerName");

void CreateLog(FName logName)
{
    FMessageLogModule& MessageLogModule = FModuleManager::LoadModuleChecked<FMessageLogModule>("MessageLog");
    FMessageLogInitializationOptions InitOptions;
    InitOptions.bShowPages = true;
    InitOptions.bShowFilters = true;
    FText LogListingName = FTEXT("Log Listing");
    MessageLogModule.RegisterLogListing(logName, LogListingName, InitOptions);
}
```

이후 게임모드의 BeginPlay에 아래를 추가합니다.

```cpp
CreateLog(LoggerName);

FMessageLog logger(LoggerName);
logger.Warning(FTEXT("A warning message from gamemode ctor"));
logger.Info(FTEXT("Info to log"));
logger.Warning(FTEXT("Warning text to log"));
logger.Error(FTEXT("Error text to log"));
```

이후 아래 메세지를 확인할 수 있습니다.

```text
MessageLoggerName: Warning: A warning message from gamemode ctor
MessageLoggerName: Info to log
MessageLoggerName: Warning: Warning text to log
MessageLoggerName: Error: Error text to log
```

<br/>

# FRotator

FRotator 를 사용하여 회전하는 액터를 만듭니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Components/ActorComponent.h"
#include "ACRotator.generated.h"


UCLASS( ClassGroup=(Custom), meta=(BlueprintSpawnableComponent) )
class TEMP_API UACRotator : public UActorComponent
{
	GENERATED_BODY()

public:	
	UACRotator()
	{
		PrimaryComponentTick.bCanEverTick = true;
	}

	// Called every frame
	virtual void TickComponent(float DeltaTime, ELevelTick TickType, FActorComponentTickFunction* ThisTickFunction) override
	{
		Super::TickComponent(DeltaTime, TickType, ThisTickFunction);

		FRotator rotator(0, GetWorld()->TimeSeconds * 10, 0);
		GetOwner()->SetActorRotation(rotator);
	}
};
```

이후 아무런 액터를 배치하고 Movevable로 변경하고 위에서 만든 ActorComponent 를 추가합니다.

이후 실행하면 회전하는 액터를 확인할 수 있습니다.

FRotator 은 인자  Pinth, Yaw, Roll 을 받습니다.

`FRotator(P, Y, R)`

<br/>

# FQuat

FQuat 를 사용하여 회전하는 액터를 만듭니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Components/ActorComponent.h"
#include "ACRotator.generated.h"


UCLASS( ClassGroup=(Custom), meta=(BlueprintSpawnableComponent) )
class TEMP_API UACRotator : public UActorComponent
{
	GENERATED_BODY()

public:	
	UACRotator()
	{
		PrimaryComponentTick.bCanEverTick = true;
	}

	// Called every frame
	virtual void TickComponent(float DeltaTime, ELevelTick TickType, FActorComponentTickFunction* ThisTickFunction) override
	{
		Super::TickComponent(DeltaTime, TickType, ThisTickFunction);

		FQuat quat = FQuat(FVector(1, 1, 0), GetWorld()->TimeSeconds * PI / 4.f);
		GetOwner()->SetActorRotation(quat);
	}
};
```

이후 아무런 액터를 배치하고 Movevable로 변경하고 위에서 만든 ActorComponent 를 추가합니다.

이후 실행하면 회전하는 액터를 확인할 수 있습니다.

FQuat 는 회전 축과 회전 각도의 크기를 받습니다. $v$ 가 회전 축 $\theta$ 가 회전 각도 크기라면 공식은 아래와 같습니다.

$x = v_x \sin \frac{\theta}{2}, y = v_y \sin \frac{\theta}{2}, z = v_z \sin \frac{\theta}{2}, w = \cos \frac{\theta}{2}$

쿼터니언의 세 가지 x, y, z 는 회전 축으로 정의하고, w는 회전할 각도 cosine 만을 가집니다.

<br/>

# FRotationMatrix

FRotationMatrix 는 일련의 `::Make` 를 사용한 매트릭스 구성을 제공합니다. 이것은 특히 한 오브젝트가 다른 오브젝트를 향하도록 하는 곳에 유용합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Components/ActorComponent.h"
#include "ACRotator.generated.h"


UCLASS( ClassGroup=(Custom), meta=(BlueprintSpawnableComponent) )
class TEMP_API UACRotator : public UActorComponent
{
	GENERATED_BODY()

public:	
	UACRotator()
	{
		PrimaryComponentTick.bCanEverTick = true;
	}

	// Called every frame
	virtual void TickComponent(float DeltaTime, ELevelTick TickType, FActorComponentTickFunction* ThisTickFunction) override
	{
		Super::TickComponent(DeltaTime, TickType, ThisTickFunction);

		FVector toFollow = target->GetActorLocation() - GetOwner()->GetActorLocation();
		FMatrix rotMatrix = FRotationMatrix::MakeFromXZ(toFollow, GetOwner()->GetActorUpVector());

		GetOwner()->SetActorRotation(rotMatrix.Rotator());
	}

	UPROPERTY(EditInstanceOnly)
	AActor* target;
};
```

이후 액터를 월드에 배치하고 다른 액터를 프로퍼티에 꼽으면 됩니다.

<br/>

# GameplayAbility API 적용

GameplayAbility API를 사용하면 특정 버튼 입력시 호출할 C++ 함수를 연결할 키 이벤트를 할당할 수 있습니다.

* UGameplayAbility::ActivateAbility
* UGameplayAbility::InputPressed
* UGameplayAbility::CheckCost
* UGameplayAbility::ApplyCost
* UGameplayAbility::ApplyCooldown

이 함수들을 재정의한다.

[projectname].Build.cs 파일을 열고 아래를 추가합니다.

```cs
PublicDependencyModuleNames.AddRange(new string[] { "GameplayAbilities", "GameplayTags", "GameplayTasks" });
```

코드를 컴파일하고, 언리얼 에디터의 툴바에서 Settings > Plugins 를 열고 Gameplay Ability 를 Enabled 한 뒤 에디터를 재시작한다.

![ga_plugin](/assets/images/ue4/ga_plugins.png)

<br/>

# GameplayAbility Trig

GameplayAbility 를 상속받는 새로운 C++ 클래스를 생성해서 각 기능들을 오버라이딩합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "Abilities/GameplayAbility.h"
#include "GameplayAbility_Attack.generated.h"

UCLASS()
class TEMP_API UGameplayAbility_Attack : public UGameplayAbility
{
	GENERATED_BODY()

    virtual bool CanActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayTagContainer* SourceTags = nullptr, const FGameplayTagContainer* TargetTags = nullptr, OUT FGameplayTagContainer* OptionalRelevantTags = nullptr) const
    {
        UE_LOG(LogTemp, Warning, TEXT("ability_attack CanActivateAbility!"));
        return true;
    }

    virtual bool CheckCost(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, OUT FGameplayTagContainer* OptionalRelevantTags = nullptr) const
    {
        UE_LOG(LogTemp, Warning, TEXT("ability_attack CheckCost!"));
        return true;
        //return Super::CheckCost( Handle, ActorInfo, OptionalRelevantTags );
    }

    virtual void ActivateAbility(const FGameplayAbilitySpecHandle Handle,
        const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo,
        const FGameplayEventData* TriggerEventData)
    {
        UE_LOG(LogTemp, Warning, TEXT("Activating ugameplayability_attack().. swings weapon!"));
        Super::ActivateAbility(Handle, ActorInfo, ActivationInfo, TriggerEventData);
    }

    virtual void InputPressed(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo)
    {
        UE_LOG(LogTemp, Warning, TEXT("ability_attack inputpressed!"));
        Super::InputPressed(Handle, ActorInfo, ActivationInfo);
    }
};
```

이후 `Content Browser 우클릭 => Miscellaneous => Data Asset` 를 선택하고 GameplayAbilitySet 를 생성한뒤 아래처럼 설정한다.

![ga_da](/assets/images/ue4/ga_da.png)

이 기능을 사용할 수 있는 새로운 Character를 만들고 Default Pawn Class 로 설정한다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Character.h"
#include "GameplayAbilitySet.h"
#include "AbilitySystemInterface.h"
#include "AbilitySystemComponent.h"
#include "Warrior.generated.h"

#define FS(x,...) FString::Printf( TEXT( x ), __VA_ARGS__ )


UCLASS()
class TEMP_API AWarrior : public ACharacter, public IAbilitySystemInterface
{
	GENERATED_BODY()

public:
    AWarrior()
    {
        PrimaryActorTick.bCanEverTick = true;
        AbilitySystemComponent = CreateDefaultSubobject<UAbilitySystemComponent>("UAbilitySystemComponent");
    }

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Stats)
    UGameplayAbilitySet* gameplayAbilitySet;

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Stats)
    UAbilitySystemComponent* AbilitySystemComponent;

    virtual UAbilitySystemComponent* GetAbilitySystemComponent() const { return AbilitySystemComponent; }

    virtual void SetupPlayerInputComponent(UInputComponent* PlayerInputComponent) override
    {
        Super::SetupPlayerInputComponent(PlayerInputComponent);
        AbilitySystemComponent->BindToInputComponent(PlayerInputComponent);

        for(const FGameplayAbilityBindInfo& BindInfo :
            gameplayAbilitySet->Abilities)
        {

            FGameplayAbilitySpec spec(
                BindInfo.GameplayAbilityClass->
                GetDefaultObject<UGameplayAbility>(),
                1, (int32)BindInfo.Command);

            FGameplayAbilitySpecHandle abilityHandle =
                AbilitySystemComponent->GiveAbility(spec);

            int32 AbilityID = (int32)BindInfo.Command;


            FGameplayAbilityInputBinds inputBinds(
                FS("ConfirmTargetting_%s_%s", *GetName(),
                    *BindInfo.GameplayAbilityClass->GetName()),
                FS("CancelTargetting_%s_%s", *GetName(),
                    *BindInfo.GameplayAbilityClass->GetName()),
                "EGameplayAbilityInputBinds",
                AbilityID, AbilityID
            );

            AbilitySystemComponent->BindAbilityActivationToInputComponent(
                PlayerInputComponent, inputBinds
            );

            AbilitySystemComponent->TryActivateAbility(
                abilityHandle, 1);
        }
    }
};
```

각 프로퍼티에 앞서 설정한 값을 넣은 후 실행합니다.

```text
LogTemp: Warning: ability_attack CanActivateAbility!
LogTemp: Warning: Activating ugameplayability_attack().. swings weapon!
LogTemp: Warning: ability_attack CheckCost!
```

이 외에도 유용한 기능들이 많으니 GameplayAbility 메뉴얼을 살펴봅시다.

<br/>

# GameplayAbility Status

UAttriuteSet 을 액터에 연결해서 여러 능력치를 표현합니다.

새로운 C++ 클래스로 GameplayAbility 를 상속받고 아래처럼 작성합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "AttributeSet.h"
#include "GameUnitAttributeSet.generated.h"

UCLASS(Blueprintable, BlueprintType)
class TEMP_API UGameUnitAttributeSet: public UAttributeSet
{
    GENERATED_BODY()

public:
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = GameUnitAttributes)
        float Hp;

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = GameUnitAttributes)
        float Mana;

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = GameUnitAttributes)
        float Speed;
};
```

이후 적용하기 위해선 캐릭터에서 아래 코드를 추가합니다.

```cpp
#include "GameUnitAttributeSet.h"

virtual void AWarrior::PostInitializeComponents() override
{
	Super::PostInitializeComponents();

	if(AbilitySystemComponent)
	{
		AbilitySystemComponent->InitStats(UGameUnitAttributeSet::StaticClass(), NULL);
	}
}
```

<br/>

# GameplayAbility Buff



```cpp
inline UGameplayEffect* ConstructGameplayEffect(FString name)
{
    return NewObject<UGameplayEffect>(GetTransientPackage(), FName(*name));
}

inline FGameplayModifierInfo& AddModifier(
    UGameplayEffect* Effect, UProperty* Property,
    EGameplayModOp::Type Op,
    const FGameplayEffectModifierMagnitude& Magnitude)
{
    int32 index = Effect->Modifiers.Num();
    Effect->Modifiers.SetNum(index + 1);
    FGameplayModifierInfo& Info = Effect->Modifiers[index];
    Info.ModifierMagnitude = Magnitude;
    Info.ModifierOp = Op;
    Info.Attribute.SetUProperty(Property);
    return Info;
}

void AWarrior::TestGameplayEffect()
{
	UGameplayEffect* RecoverHP = ConstructGameplayEffect("RecoverHP");

	UProperty* hpProperty = FindFieldChecked<UProperty>(
		UGameUnitAttributeSet::StaticClass(),
		GET_MEMBER_NAME_CHECKED(UGameUnitAttributeSet, Hp));

	AddModifier(RecoverHP, hpProperty, EGameplayModOp::Additive, FScalableFloat(50.f));

	RecoverHP->DurationPolicy = EGameplayEffectDurationType::HasDuration;
	RecoverHP->DurationMagnitude = FScalableFloat(10.f);

	RecoverHP->ChanceToApplyToTarget = 1.f;

	RecoverHP->Period = 0.5f;

	FActiveGameplayEffectHandle recoverHpEffectHandle =
		AbilitySystemComponent->ApplyGameplayEffectToTarget(
			RecoverHP, AbilitySystemComponent, 1.f);

	FOnActiveGameplayEffectRemoved* ep = AbilitySystemComponent->
		OnGameplayEffectRemovedDelegate(recoverHpEffectHandle);

	if(ep)
	{
		ep->AddLambda([]()
		{
			UE_LOG(LogTemp, Warning, TEXT("Recover effect has been removed."), 1);
		});
	}
}
```

<br/>

# GameplayAbility Task

GameplayAbility Task 는 게임 플레이 기능을 재사용 가능한 오브젝트로 만들 때 사용합니다. UGameplayTask 를 상속 후 구현하고 싶은 멤버 함수를 재정의 하면 됩니다.

UGameplayTask를 상속받는 새로운 클래스를 정의하고

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameplayTask.h"
#include "Particles/ParticleSystem.h"
#include "Kismet/GameplayStatics.h"
#include "GameplayTask_CreateParticles.generated.h"

UCLASS()
class TEMP_API UGameplayTask_CreateParticles : public UGameplayTask
{
	GENERATED_BODY()
	
public:
    virtual void Activate()
    {
        Super::Activate();

        UGameplayStatics::SpawnEmitterAtLocation(GetWorld(), ParticleSystem, Location);

    }

    UFUNCTION(BlueprintCallable, Category = "GameplayTasks", meta = (AdvancedDisplay = "TaskOwner", DefaultToSelf = "TaskOwner", BlueprintInternalUseOnly = "TRUE"))
        static UGameplayTask_CreateParticles* ConstructTask(
            TScriptInterface<IGameplayTaskOwnerInterface> TaskOwner,
            UParticleSystem* particleSystem,
            FVector location)
    {
        IGameplayTaskOwnerInterface& AsTaskOwner = *TaskOwner;

        UGameplayTask_CreateParticles* task = NewTask<UGameplayTask_CreateParticles>(TaskOwner);

        if(task)
        {
            task->ParticleSystem = particleSystem;
            task->Location = location;
        }

        return task;
    }

    UParticleSystem* ParticleSystem;
    FVector Location;
};
```

이후 이 이벤트가 등록될 캐릭터에 IGameplayTaskOwnerInterface를 상속받고 아래처럼 작성하면 됩니다.

```cpp
UCLASS()
class TEMP_API AWarrior : public ACharacter, public IAbilitySystemInterface, public IGameplayTaskOwnerInterface
{
	...
public:
    AWarrior()
    {
        PrimaryActorTick.bCanEverTick = true;
        AbilitySystemComponent = CreateDefaultSubobject<UAbilitySystemComponent>("UAbilitySystemComponent");
        GameplayTasksComponent = CreateDefaultSubobject<UGameplayTasksComponent>("UGameplayTasksComponent");
    }

	virtual void PostInitializeComponents() override
    {
		...

        UGameplayTask_CreateParticles* task =
            UGameplayTask_CreateParticles::ConstructTask(this, particleSystem, FVector(200.f, 0.f, 200.f));


        if(GameplayTasksComponent && task)
        {
            GameplayTasksComponent->AddTaskReadyForActivation(*task);
        }
    }

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Stats)
	UGameplayTasksComponent* GameplayTasksComponent;

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Stats)
	UParticleSystem* particleSystem;

	virtual UGameplayTasksComponent* GetGameplayTasksComponent(const UGameplayTask& Task) const { return GameplayTasksComponent; }

	virtual void OnTaskActivated(UGameplayTask& Task) {}
	virtual void OnTaskDeactivated(UGameplayTask& Task) {}

	virtual AActor* GetOwnerActor(const UGameplayTask* Task) const
	{
		return Task->GetOwnerActor();
	}
}
```

이후 캐릭터를 BP로 생성한 뒤 Stat 카테고리의 멤버를 지정해줍니다.

![ga_tk](/assets/images/ue4/ga_tk.png)

<br/>

# HTTP Request

HTTP 요청이 필요한 서비스를 유지하려는 경우 HTTP API 를 사용해서 요청을 할 수 있습니다.

우선 모듈을 추가하고

```cs
PublicDependencyModuleNames.AddRange(new string[] { "HTTP" });
```

아래 코드를 추가하고 호출합니다.

```cpp
#include "Runtime/Online/HTTP/Public/HttpManager.h" 
#include "Runtime/Online/HTTP/Public/HttpModule.h" 
#include "Runtime/Online/HTTP/Public/HttpRetrySystem.h" 
#include "Runtime/Online/HTTP/Public/Interfaces/IHttpResponse.h"
using namespace FHttpRetrySystem;

void TestHttp()
{
	TSharedRef<IHttpRequest> http = FHttpModule::Get().CreateRequest();

	http->OnRequestProgress().BindLambda(
		[this](FHttpRequestPtr request, int32 sentBytes, int32 receivedBytes)
		-> void
	{
		int32 contentLen = request->GetResponse()->GetContentLength();
		float percentComplete = 100.f * receivedBytes / contentLen;

		UE_LOG(LogTemp, Warning, TEXT("Progress sent=%d bytes / received=%d/%d bytes [%.0f%%]"), sentBytes, receivedBytes, contentLen, percentComplete);

	});


	FHttpRequestCompleteDelegate& delegate = http->OnProcessRequestComplete();
	delegate.BindLambda(
		[](FHttpRequestPtr request, FHttpResponsePtr response, bool success) -> void
	{
		UE_LOG(LogTemp, Warning, TEXT("Http response %d, %s"),
			response->GetResponseCode(), *response->GetContentAsString());
	});

	http->SetURL(TEXT("https://jjangdoll.github.io/"));

	http->ProcessRequest();
}
```