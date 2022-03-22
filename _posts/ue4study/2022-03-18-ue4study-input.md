---
title: "UE4 Input System"

categories:
  - UE4STUDY


tags:
  - UE4
  - input system
  - axis mapping
  - action mapping

toc: true
toc_label: UE4 Key Input
toc_sticky: true

sidebar_main: true
---

입력 시스템은 게임에서 매우 매우 중요합니다. 언리얼은 키보드, 마우스, 게임패드 까지 지원하면서 다양한 선택권을 제공합니다.

<br/>

# Input System

입력 매핑은 두 종류로 축 매핑(Axis Mapping)과 액션 매핑(Action Mapping)이 존재합니다. 

여기서는 프로젝트 설정을 바꾸지 않으면서 C++ 로만 키 입력을 추가해봅니다.

<br/>

## Axis Mapping

축 매핑은 입력 값의 수치(기울기)를 얻을 수 있습니다. 예를 들어 W를 누르는 동안에는 전진을 하는 입력은 축 매핑입니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Character.h"
#include "GameFramework/PlayerInput.h"
#include "MyCharacter.generated.h"

UCLASS()
class TEMP_API AMyCharacter : public ACharacter
{
	GENERATED_BODY()

public:	
	void Forward(float amount)
	{
		AddMovementInput(GetActorForwardVector(), amount);
	}

	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override
	{
		FInputAxisKeyMapping forwardKey("Forward", EKeys::W, 1.f);

		GetWorld()->GetFirstPlayerController()->PlayerInput->AddAxisMapping(forwardKey);

		PlayerInputComponent->BindAxis("Forward", this, &AMyCharacter::Forward);
	}
};
```

<br/>

### Input Normalize

축 입력으로 두 키를 같이 누르게 된다면 피타고라스 정리에 따라 1이 넘어갈 수 밖에 없습니다. 그렇기 때문에 입력 값을 정규화 하는 과정이 필요합니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Character.h"
#include "GameFramework/PlayerInput.h"
#include "MyCharacter.generated.h"

UCLASS()
class TEMP_API AMyCharacter: public ACharacter
{
	GENERATED_BODY()

private:
	FVector2D LastInput;

public:
	void Forward(float amount)
	{
		LastInput.Y += amount;
	}

	void Right(float amount)
	{
		LastInput.X += amount;
	}

	virtual void Tick(float DeltaTime)
	{
		Super::Tick(DeltaTime);

		float len = LastInput.Size();
		if(len > 1.f)
		{
			LastInput /= len;
		}
		AddMovementInput(GetActorForwardVector(), LastInput.Y);
		AddMovementInput(GetActorRightVector(), LastInput.X);
		LastInput = FVector2D(0.f, 0.f);
	}

	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override
	{
		FInputAxisKeyMapping forwardKey("Forward", EKeys::W, 1.f);
		FInputAxisKeyMapping rightKey("Right", EKeys::D, 1.f);

		GetWorld()->GetFirstPlayerController()->PlayerInput->AddAxisMapping(forwardKey);
		GetWorld()->GetFirstPlayerController()->PlayerInput->AddAxisMapping(rightKey);

		PlayerInputComponent->BindAxis("Forward", this, &AMyCharacter::Forward);
		PlayerInputComponent->BindAxis("Right", this, &AMyCharacter::Right);
	}
};
```

<br/>

## Action Mapping

액션 매핑은 키 입력을 얻을 수 있으며 Ctrl, Shift, Alt, Cmd 키와 조합해서 사용할 수 있습니다. 예를 들어 스페이스바를 누르면 점프를 하는 기능은 액션 매핑입니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Character.h"
#include "GameFramework/PlayerInput.h"
#include "MyCharacter.generated.h"

UCLASS()
class TEMP_API AMyCharacter : public ACharacter
{
	GENERATED_BODY()

public:	
	virtual void SetupPlayerInputComponent(class UInputComponent* PlayerInputComponent) override
	{
		FInputActionKeyMapping jump("Jump", EKeys::SpaceBar, 0, 0, 0, 0);

		GetWorld()->GetFirstPlayerController()->PlayerInput->AddActionMapping(jump);

		PlayerInputComponent->BindAction("Jump", EInputEvent::IE_Pressed, this, &ACharacter::Jump);
	}
};
```

<br/>