---
title: "UE4 Collision"

categories:
  - UE4STUDY


tags:
  - UE4
  - collision

toc: true
toc_label: UE4 Collision
toc_sticky: true

sidebar_main: true
---

세 가지 유형의 충돌 처리 방법이 있습니다.

* Ignore : 아무런 알림 없이 서로 통과하는 충돌
* Overlap : OnBeginOverlap, OnEndOverlap 이벤트를 발생시키는 충돌. Overlap 설정을 하면 오브젝트가 서로 겹칠 수 있다.
* Block : 모든 충돌에 대해 오브젝트의 겹침을 허용하지 않는다.

# Collision

Detail 패널의 Collision 카테고리에서 설정할 수 있습니다.

<br/>

## Ignore

![Collision_Ignore](/assets/images/ue4/collision_ignore.png)


이렇게 무시된 충돌은 어떤 이벤트도 발생하지 않고 오브젝트 간 충돌도 막지 않는다. 두 오브젝트가 충돌 할 때 한 오브젝트라도 Ignore 라면 서로 무시한다.

<br/>

## Overlap

이번엔 플레이어만 특정 객체와 충돌할 수 있게 해보자

`Settings->Project Settings->Collision` 메뉴에서 Item 오브젝트의 충돌 기본 요소를 위한 고유 충돌 채널을 만듭니다.

![Collision_Overlap_1](/assets/images/ue4/collision_overlap_1.png)

![Collision_Overlap_2](/assets/images/ue4/collision_overlap_2.png)

![Collision_Overlap_3](/assets/images/ue4/collision_overlap_3.png)

이후 CPP 캐릭터에서 아래처럼 코드를 추가합니다.

```cpp
UCLASS()
class TEMP_API AMyCharacter: public ACharacter
{
	GENERATED_BODY()

private:
	FVector2D LastInput;

public:
	UFUNCTION(BlueprintNativeEvent, Category = Collision)
	void OnOverlapsBegin(UPrimitiveComponent* Comp, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult);

	UFUNCTION(BlueprintNativeEvent, Category = Collision)
	void OnOverlapsEnd(UPrimitiveComponent* Comp, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex);

	virtual void PostInitializeComponents() override
	{
		Super::PostInitializeComponents();

		if(RootComponent)
		{
			GetCapsuleComponent()->OnComponentBeginOverlap.AddDynamic(this, &AMyCharacter::OnOverlapsBegin);
			GetCapsuleComponent()->OnComponentEndOverlap.AddDynamic(this, &AMyCharacter::OnOverlapsEnd);
		}
	}
};
```

이후 `BlueprintNativeEvent` 를 cpp 파일에서 구현해줍니다.

```cpp
#include "MyCharacter.h"
#include "Components/PrimitiveComponent.h"
#include "Engine/EngineTypes.h"

void AMyCharacter::OnOverlapsBegin_Implementation(UPrimitiveComponent* Comp, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult)
{
    UE_LOG(LogTemp, Warning, TEXT("Overlap Begin"));
}

void AMyCharacter::OnOverlapsEnd_Implementation(UPrimitiveComponent* Comp, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex)
{
    UE_LOG(LogTemp, Warning, TEXT("Overlap End"));
}
```

<br/>

## Block

![Collision_Block](/assets/images/ue4/collision_block.png)

마찬가지로 콜리전 프리셋을 변경해준다. 블록을 하기 위해선 두 오브젝트 모두 블록하도록 설정해야 한다.

`OnComponentHit` 함수를 재정의 해서 충돌시 이벤트를 트리거 할 수 있다.