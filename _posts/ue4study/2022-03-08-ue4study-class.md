---
title: "UE4 Creating Classess"

categories:
  - UE4STUDY


tags:
  - UE4
  - UCLASS
  - UPROPERTY
  - UFUNCTION
  - USTRUCT
  - UENUM

toc: true
toc_label: UE4 Creating Classes
toc_sticky: true

sidebar_main: true
---

UCLASS 매크로를 제대로 구성하면 UCLASS를 복제하거나 재사용할 수 있게 만들어서 C++ 오브젝트를 블루프린트에서도 사용할 수 있다. 이렇게되면 코드에 직접 접근하지 않고도 수정을 할 수 있으므로 유용하다.

텍스트 필드, 슬라이더, 모델 선택 상자와 같이 편리한 UI 위젯으로 시각적 편집 속성을 사용할 수 있다. 또한 블루프린트 내에서 호출 가능한 UFUNCTION 도 사용할 수 있다.

# UCLASS 생성

UE에서 UObject 상속받는 클래스를 생성하게 되면 아래와 같은 헤더 파일이 생성된다.

```cpp
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "UObject/NoExportTypes.h"
#include "SampleUClass.generated.h"

/**
 * 
 */
UCLASS()
class TEMP_API USampleUClass : public UObject
{
	GENERATED_BODY()
	
};

```

UE는 UPROPERTY, UFUNCTION, UCLASS 매크로를 사용한 결과로 생성된 많은 양의 코드를 생성하고 관리한다. 생성된 코드는 `[ClassName].generated.h` 에 저장되고 **반드시 이 파일을 추가해야하며 마지막에 #include 해야 한다**. 

<br/>

# Blueprint Inheritance

## UCLASS Keyword

UCLASS 의 동작에 영향을 주는 다수의 키워드가 존재한다.

| Keyword | Effective |
|---------------|--------|
| Blueprintable | BLueprint 서브클래스를 생성 가능 |
| BlueprintType | Detail 패널의 Variables 에서 블루프린트 변수 생성 가능 |

이 외에도 수 많은 키워드들이 있다.

[UClass Specifier](https://docs.unrealengine.com/4.27/ko/ProgrammingAndScripting/GameplayArchitecture/Classes/Specifiers/)

<br/>

## Inheritance Blueprint

블루프린트에서 상속 가능한 클래스로 만들기 위해선 아래처럼 적절한 키워드를 추가해줘야 한다.

```cpp
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "UObject/NoExportTypes.h"
#include "SampleUClass.generated.h"

/**
 * 
 */
UCLASS(Blueprintable)                           <- 추가
class TEMP_API USampleUClass : public UObject
{
	GENERATED_BODY()
	
};
```

이후엔 블루프린트로 상속을 받을 수 있다.

<br/>

# UPROPERTY

## UPROPERTY KEYWORD

UPROPERTY 의 동작에 영향을 주는 다수의 키워드가 존재한다.

|Keyword|Effective|
|-------|---------|
|EditAnywhere| 프로퍼티를 어디서든 직접 수정할 수 있다. |
|EditDefaultOnly | 디폴트에서만 값을 수정할 수 있다. |
|EditInstanceOnly | 인스턴스된 객체에서만 값을 수정할 수 있다. |
|BlueprintReadWrite| 다이어그램에서 읽고 쓸 수 있다. 반드시 public 으로 선언해야만 한다. |
|BlueprintReadOnly | 다이어그램에서 읽기만 가능하다.|
|Category | Detail 패널에서 연관된 프로퍼티끼리 묶을 수 있다. |


[UPROPERTY Specifier](https://docs.unrealengine.com/4.26/en-US/ProgrammingAndScripting/GameplayArchitecture/Properties/Specifiers/)

<br/>

## Editable Blueprint Member

블루프린트에서 접근과 수정이 가능한 멤버를 생성할 수 있다.


```cpp
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "UObject/NoExportTypes.h"
#include "SampleUClass.generated.h"

/**
 * 
 */
UCLASS(Blueprintable)                           
class TEMP_API USampleUClass : public UObject
{
	GENERATED_BODY()
	
public:
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Stats)
    float HP;    

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Stats)
    float MP;

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Stats)
    FString Name;
};
```

<br/>

## UCLASS property

UPROPERTY 타입으로 ULASS 를 지정할 수 있다. **하지만 절대 new를 사용해 직접 할당하면 안된다.**

UClass 멤버 생성은 TSubClassOf 변수를 사용하거나 FStringClassReference 를 사용한다.

```cpp
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "UObject/NoExportTypes.h"
#include "SampleUClass.generated.h"

/**
 * 
 */
UCLASS(Blueprintable)                           
class TEMP_API USampleUClass : public UObject
{
	GENERATED_BODY()
	
public:
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Unit)
    TSubClassOf<UObject> UClassOfPlayer;

    UPROPERTY(EditAnywhere, meta=(MetaClass="GameMode"), Category = Unit)
    FStringClassReference UClassGameMode;
};
```
<br/>

### TSubclassOf

`TSubclassOf<[ClassName]>` 를 사용하면 블루프린트에서 편집할 때 드랍다운 메뉴에서 해당 ClassName 을 갖는 멤버만 보이게 된다.

### FStringClassReference

MetaClass 태그는 MetaClass 에서 파생될 것으로 예상되는 기본 C++ 클래스를 가르킨다. 이를 통해 해당 C++ 클래스에서 파생된 블루프린트로 제한할 수 있다.

<br/>

# UObject Instance

UCLASS 를 인스턴스 하기 위해선 전용 팩토리 함수를 호출해야 한다. 이 팩토리 함수를 호출함으로써 UE4에서 오브젝트의 메모리 관리를 수행해 오브젝트가 삭제될 때 필요한 처리를 할 수 있도록 한다. 이처럼 불필요한 메모리에 대한 참조가 있는 포인터가 사라지도록 돕는 것을 GB(Garbage Collection) 이라고 부른다.

UObject에서 파생된 클래스(AActor 파생 클래스 제외)를 인스턴스 할 때는 `ConstructObject<>` 나 `NewObject<>` 를 사용한다. **절대 new를 사용하지 말자**

```cpp
[ObjectType*] object = ConstructObject<[ObjectType]>([UClassReference]);
```

## Example

```cpp
USampleUClass* newObject = NewObject<USampleUClass>((UObject*)GetTransientPackage(), USampleUClass::StaticClass());
```

<br/>

# UObject Destroy

단일 함수 `[objectInstance]->ConditionalBeginDestroy()` 를 호출해 제거를 시작한다.

```cpp
newObject->ConditionalBeginDestroy();
```

## GC 호출 간격

`\config\BaseEngine.ini` 에서 아래 `gc.TimeBetweenPurgingPendingKillObjects=61.1` 옵션으로 찾을 수 있다.

> 지나치게 많이 호출하면 렉 현상을 유발할 수 있으니 주의

<br/>

# USTRUCT

USTRCT 를 생성하고 BP 에서 보는 방법은 아래와 같다.

```cpp
USTRUCT(Blueprintable)
struct FMyUStruct
{
    GENERATED_USTRUCT_BODY()

public:
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = HUD)
    UTexture* Texture;

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = HUD)
    FLinearColor Color;
};
```

```cpp
UCLASS(Blueprintable, BlueprintType)
class UE_CPP_SANDBOX_API USampleUClass : public UObject
{
    GENERATED_BODY()

public:
    UMySampleObject();

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = HUD)
    FMyUStruct myUStrt;
};
```

<br/>

# UENUM

C++ enum 인스턴스는 매우 유용하다. UE4 에서도 UENUM()이라는 커스텀 열거형을 지원한다.

```cpp
UENUM()
enum CharacterStatus
{
    STPOPED         UMETA(DisplayName = "Stopped"),
    MOVING          UMETA(DisplayName = "Moving"),
    ATTACKING   UMETA(DisplayName = "Attacking")
};
```

```cpp
UCLASS(Blueprintable, BlueprintType)
class UE_CPP_SANDBOX_API UMySampleObject : public UObject
{
    GENERATED_BODY()

public:
    UMySampleObject();

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Status)
    TEnumAsByte<CharacterStatus> status;
};
```

<br/>


----

* https://docs.unrealengine.com/4.27/ko/ProgrammingAndScripting/GameplayArchitecture/Classes/Specifiers/
* https://docs.unrealengine.com/4.26/en-US/ProgrammingAndScripting/GameplayArchitecture/Properties/Specifiers/

