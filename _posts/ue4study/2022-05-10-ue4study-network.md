---
title: "UE4 Network"

categories:
  - UE4STUDY


tags:
  - UE4
  - UE Network


toc: true
toc_label: UE4 Network
toc_sticky: true

sidebar_main: true
---

UE4에서 네트워크 작업을 진행합니다.

<br/>

# 다수의 플레이어 테스트

다수의 플레이어가 게임을 진행하도록 할 때 쉬운 테스트 방법을 알아봅니다.

언리얼 툴바의 Play 부분의 화살표를 열어 `Number of Players` 의 숫자를 변경합니다.

![net_mp](/assets/images/ue4/net_mp.png)

<br/>

# 네트워크를 통한 속성 복제

각 플레이어마다 몇 회의 점프를 뛸 수 있는지 저장하고 확인합니다.

(ThirdPersonCPP 템플릿을 사용합니다.)

캐릭터 클래스에 아래 내용을 추가합니다.

Header File

```cpp
UPROPERTY(Replicated, EditAnywhere)
uint32 JumpCount;

virtual void Jump() override;

```

CPP File

```cpp
#include "Net/UnrealNetwork.h"

void ATemp2Character::Jump()
{
	Super::Jump();

	JumpCount++;

	if(GetLocalRole() == ROLE_Authority)
	{
		GEngine->AddOnScreenDebugMessage(-1, 5.0f, FColor::Green, FString::Printf(TEXT("%s called Jump %d times!"), *GetName(), JumpCount));
	}
}

void ATemp2Character::SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent)
{
	// Set up gameplay key bindings
	check(PlayerInputComponent);
	PlayerInputComponent->BindAction("Jump", IE_Pressed, this, &ATemp2Character::Jump); // => 오버라이딩 된 함수로 변경
}
```

<br/>

# 네트워크를 통한 함수 복제

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "Components/SphereComponent.h"
#include "CollectableObject.generated.h"

UCLASS()
class TEMP2_API ACollectableObject : public AActor
{
	GENERATED_BODY()
	
public:	
    ACollectableObject()
    {
        PrimaryActorTick.bCanEverTick = true;
        bReplicates = true;
        USphereComponent* SphereCollider = CreateDefaultSubobject<USphereComponent>(TEXT("SphereComponent"));
        RootComponent = SphereCollider;
        SphereCollider->InitSphereRadius(64.0f);
        SphereCollider->OnComponentBeginOverlap.AddDynamic(this, &ACollectableObject::OnBeginOverlap);
        UStaticMeshComponent* SphereVisual = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Static Mesh"));
        SphereVisual->SetupAttachment(RootComponent);
        auto MeshAsset = ConstructorHelpers::FObjectFinder<UStaticMesh>(TEXT("StaticMesh'/Engine/BasicShapes/Sphere.Sphere'"));
        if(MeshAsset.Object != nullptr)
        {
            SphereVisual->SetStaticMesh(MeshAsset.Object);
        }
        SphereVisual->SetWorldScale3D(FVector(0.5f));
    }

    UFUNCTION()
        void OnBeginOverlap(class UPrimitiveComponent*
            HitComp,
            class AActor* OtherActor,
            class UPrimitiveComponent*
            OtherComp,
            int32 OtherBodyIndex,
            bool bFromSweep,
            const FHitResult& SweepResult)
    {
        if(GetLocalRole() == ROLE_Authority)
        {
            UpdateScore(1);
            Destroy();
        }
    }

    // Our server function to update the score.
    UFUNCTION(Reliable, Server, WithValidation)
    void UpdateScore(int32 Amount);

    void UpdateScore_Implementation(int32 Amount)
    {
        if(GEngine)
        {
            GEngine->AddOnScreenDebugMessage(-1, 5.0f,
                FColor::Green,
                "Collected!");
        }
    }

    bool UpdateScore_Validate(int32 Amount)
    {
        return true;
    }
};
```

이후 액터를 월드에 배치한 이유 캐리겉로 먹으면 서버에서만 메시지가 출력됩니다.

<br/>