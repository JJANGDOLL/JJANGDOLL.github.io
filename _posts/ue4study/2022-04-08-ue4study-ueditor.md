---
title: "UE4 BP & CPP"

categories:
  - UE4STUDY


tags:
  - UE4
  - blueprint
  - cpp
  - ueeditor


toc: true
toc_label: UE4 BP & Editor
toc_sticky: true

sidebar_main: true
---

언리얼은 개발자가 만든 오브젝트를 기획자가 커스터마이즈가 가능한 강점이 있습니다. CPP과 BP 사이의 통신을 정리합니다.

<br/>

# BP에서 변수로 사용 가능한 CPP 클래스나 구조체

```cpp
#pragma once

#include "CoreMinimal.h"
#include "UObject/NoExportTypes.h"
#include "MyObject.generated.h"

UCLASS(BlueprintType)
class TEMP_API UMyObject : public UObject
{
	GENERATED_BODY()
	
public:
	UPROPERTY()
	int32 Cost;

	UPROPERTY()
	bool isBuild;

	UPROPERTY()
	FString Name;
};
```

이후 BP에선 이 클래스를 변수로써 사용할 수 있게됩니다.

![bpcpp_var](/assets/images/ue4/bpcpp_var.png)

다만, 문제가 있다면 메소드나 프로퍼티에 접근이 불가능합니다.

<br/>

# CPP 클래스를 BP로 서브클래싱

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "CPPBase.generated.h"

UCLASS(Blueprintable)
class TEMP_API ACPPBase : public AActor
{
	GENERATED_BODY()
};
```

이후 BP에서 이 클래스를 서브클래싱 할 수 있게됩니다.

![subclass](/assets/images/ue4/bpcpp_subclass.png)

하지만, 여전히 메소드나 프로퍼티에 접근이 불가능합니다.

<br/>

# BP에서 호출 가능한 CPP 메소드

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "CPPBase.generated.h"

UCLASS()
class TEMP_API ACPPBase : public AActor
{
	GENERATED_BODY()

public:
	UFUNCTION(BlueprintCallable)
	void Hello() {};
};
```

이후 BP에서 해당 객체에 접근하면 CPP에서 구현한 함수를 볼 수 있습니다.

![bpcallable](/assets/images/ue4/bp_cppblueprintcallble.png)

<br/>

# BP에서 구현 가능한 CPP 메소드

CPP에서 특정 함수를 BP에서 구현하도록 위임시킬 수 있습니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "CPPBase.generated.h"

UCLASS()
class TEMP_API ACPPBase : public AActor
{
	GENERATED_BODY()

public:
	UFUNCTION(BlueprintImplementableEvent)
	void Hello();
};
```

이후 BP에서 서브클래싱 하면 오버라이딩에서 해당 함수를 구현할 수 있게됩니다.

이 때, 해당 CPP에서는 구현이 없어야합니다.

![bpdele](/assets/images/ue4/bpcpp_delegate.png)

<br/>

# BP에 멀티캐스트 델리게이트 노출시키기

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "CPPBase.generated.h"

DECLARE_DYNAMIC_MULTICAST_DELEGATE(FOnDelegate);

UCLASS()
class TEMP_API ACPPBase : public AActor
{
	GENERATED_BODY()

public:
	UPROPERTY(BlueprintAssignable)
	FOnDelegate OnDele;
};
```

이후 BP에서 원하는 이벤트를 할당할 수 있게 됩니다.

![bpassign](/assets/images/ue4/bpcpp_bpassignable.png)

<br/>

# BP에서 사용 가능한 CPP 열거형

열거형은 최곱니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "CPPBase.generated.h"

UENUM(BlueprintType)
enum CPPEnum
{
	IDLE,
	RUN,
	HIT,
	ATTACK
};

UCLASS()
class TEMP_API ACPPBase : public AActor
{
	GENERATED_BODY()

public:
	UPROPERTY(BlueprintReadWrite)
	TEnumAsByte<CPPEnum> Cppenum;
};
```

이러면 변수로도, 값 수정도 가능해지는 enum이 탄생했습니다.

![bpcppenum](/assets/images/ue4/bpcpp_enum.png)

<br/>

# UPROPERTY 지정자

UPROPERTY 지정자에는 VisibleDefaultOnly, BlueprintReadWrite 등등... 여러가지가 많은데 요약해보면 이렇다.

갯수가아닌 역할별로 나누었으며 조합을 했을때 의미를 쉽게 파악할? 수 있다.

| 지정자 | 역할 |
|-------|--------|
| Instace | 인스턴스 된 객체만 |
| Default | 서브클래싱 된 BP의 디폴트 창에서(더블 클릭하면 나오는 그 창, Blueprint옵션과 같이 사용해야합니다.) |
| Blueprint | BP 서브클래싱에서 값을 어떻게 관리할건지 |
| Visible | 해당 프로퍼티가 있음을 보여줍니다. | 
| Read | 값을 보여줍니다.(Visible이 있으면 프로퍼티 자체가 안보여서 알 수가 없습니다.) |
| ReadWrite | 값을 보여주고 쓸 수도 있습니다. | 

크게 Instace 상황에 적용되는 옵션, Default 랑 BP 상황에 적용되는 옵션과 값의 수정 권한에 대한 옵션으로 분류됩니다.

예전에 누가 헷갈렸는데 옵션은 서로 충돌나서 빌드가 안 될 수 있습니다. `	UPROPERTY(BlueprintReadOnly, BlueprintReadWrite)
`이런식으로 쓰면 섭섭합니다.

<br/>

# 프로퍼티 값 변경시 트리거 되는 이벤트

프로퍼티의 변경에 즉각 수정내용이 반영되도록 설정하고 싶을 수도 있습니다.

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "CPPBase.generated.h"

UCLASS()
class TEMP_API ACPPBase : public AActor
{
	GENERATED_BODY()

public:
	UPROPERTY(EditInstanceOnly)
	int32 Var;

	virtual void PostEditChangeProperty(FPropertyChangedEvent& PropertyChangedEvent) override
	{
		UE_LOG(LogTemp, Display, TEXT("AAAAA"));
	}

};
```
