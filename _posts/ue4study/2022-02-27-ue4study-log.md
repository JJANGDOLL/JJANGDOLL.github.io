---
title: "UE4 Logging"

categories:
  - UE4STUDY


tags:
  - UE4
  - UE_LOG

toc: true
toc_label: UE4 Logging
toc_sticky: true

sidebar_main: true
---

로깅은 이벤트, 함수 호출, 변수 값 등을 기록하는 것을 의미합니다. 일반적으로는 로그 파일에 텍스트 형태로 저장됩니다. 코드의 특정 순간에 자세한 정보를 확인할 수 있으므로 디버깅할 때 가장 중요한 도구입니다. 특정 코드 블럭이 실행 중인지, 함수 인자에 원하는 값이 넘어오는지 등을 검사하거나 잠재적인 문제를 보고합니다.

<br/>

# Logging Syntax

```cpp
UE_LOG(LogTemp, Warning, TEXT("Hello"));
```

> *로그 구문을 사용해도 아무런 메세지가 없다면, 프로젝트 컴파일이 동작중인지 확인해봅시다.*

**UE_LOG** 는 로그 메세지를 로그 파일에 저장하는 매크로입니다. <br/>

첫 번째 인자는 로그 카테고리(logging category)의 이름입니다. 엔진에 이미 많은 카테고리가 빌드 및 정의되어 있으며 CoreGlobals.h 에서 확인할 수 있습니다. 또한, 자신이 원하는 카테고리를 추가하는 것도 가능합니다. <br/>

두 번째 인자는 상세 수준 레벨(Verbosity level)입니다. 

세 번재 인자는 출력하고자 하는 텍스트 메시지 입니다.  추가로 printf 포맷 지정자를 사용하는 경우 인자가 더 늘어날 수 있습니다.

# Formatting Example

## Logging an FString

```cpp
UE_LOG(LogTemp, Warning, TEXT("The Actor's name is %s"), *YourActor->GetName());
```

<br/>



## Logging a Bool

```cpp
UE_LOG(LogTemp, Warning, TEXT("The boolean value is %s"), ( bYourBool ? TEXT("true") : TEXT("false") ));
```

<br/>

## Logging an Integer

```cpp
UE_LOG(LogTemp, Warning, TEXT("The integer value is: %d"), YourInteger);
```

<br/>

## Logging a Float

```cpp
UE_LOG(LogTemp, Warning, TEXT("The float value is: %f"), YourFloat);
```

<br/>

## Logging an FVector

```cpp
UE_LOG(LogTemp, Warning, TEXT("The vector value is: %s"), *YourVector.ToString());
```

<br/>

## Logging with Multiple Specifiers

```cpp
UE_LOG(LogTemp, Warning, TEXT("Current values are: vector %s, float %f, and integer %d"), *YourVector.ToString(), YourFloat, YourInteger);
```

<br/>

# Accesing Logs

로그를 확인하는 다양한 방법이 있습니다.

* PIE 가 종료되면, 해당 세션에 대한 모든 전체 로그(full log)를 `YourProjectName/Saved/Logs` 폴더에서 찾을 수 있습니다. 과거의 세션에 대한 로그도 해당 위치에 있을 수 있습니다. 
* Play 중에 Output Log 탭에서 로그를 확인할 수 있습니다.
* 실행파일 인 경우, 이름 끝에 -Log 를 사용하여 바로가기를 만들어 실행 파일을 시작할 때 로그를 열 수 있습니다.
* 게임이 실행 중인 경우, `~(물결표)` 를 눌러 콘솔을 열고 `showlog` 명령어를 입력해 로그에 엑세스 할 수 있습니다.

<br/>

# Log Verbosity Levels

상황에 알맞게 로그를 더 자세히 보거나, 간략히 보는 것이 유용합니다. Log Verbosity 를 사용해 로그의 수준을 쉽게 결정할 수 있습니다. 

| Verbosity Level | Printed in Console? | Printed in Editor's Log? |                      Notes                       |
|-----------------|---------------------|--------------------------|--------------------------------------------------|
| Fatal           | Yes                 | N/A                      | Crashes the session, even if logging is disabled |
| Error           | Yes                 | Yes                      | Log text is coloured red                         |
| Warning         | Yes                 | Yes                      | Log text is coloured yellow                      |
| Display         | Yes                 | Yes                      | Log text is coloured grey                        |
| Log             | No                  | Yes                      | Log text is coloured grey                        |
| Verbose         | No                  | No                       |                                                  |
| VeryVerbose     | No                  | No                       |                                                  |

<br/>

# Custom Log Category

로그 카테고리를 직접 만든다면 Output Log 창의 많은 로그에서 원하는 정보만 골라서 표시할 수 있습니다. 새로운 핵심 시스템이나 게임모드, 레벨 등에 자주 사용됩니다.

형식은 아래와 같습니다.

`DECLARE_LOG_CATEGORY_EXTERN(CategoryName, DefaultVerbosity, CompileTimeVerbosity)`

* CategoryName : 원하는 카테고리 이름
* DefaultVerbosity : ini 파일이나  CommandLine에서 지정되지 않은 경우 기본 Verbosity Level.
* CompileTimeVerbosity : 코드에서 컴파일 할 최대 Verbosity Level. 이보다 자세한 내용은 컴파일이 안됩니다.

아래 코드처럼 추가해서 새로운 로그 카테고리를 정의할 수 있습니다. 필요한 파일의 top level에 #include 해서 사용하세요.

## Header File

```cpp
// LogCustom : 원하는 카테고리 이름

DECLARE_LOG_CATEGORY_EXTERN(LogCustom, Log, All);
```

## Cpp File

```cpp
DEFINE_LOG_CATEGORY(LogCustom);
```

<br/>


# Custom Log Category Example

아래처럼 작성하여 다양한 정보를 출력해주는 매크로를 작성할 수 있습니다.

```cpp
DECLARE_LOG_CATEGORY_EXTERN(LogCustom, Log, All)
#define LOG_CALLINFO (FString(__FUNCTION__) + TEXT("(") + FString::FromInt (__LINE__) + TEXT(")"))
#define LOG_S(Verbosity) UE_LOG(LogCustom, Verbosity,  TEXT("%s"), *LOG_CALLINFO)
#define LOG(Verbosity, Format, ...) UE_LOG(LogCustom, Verbosity, TEXT("%s%s"), *LOG_CALLINFO, *FString::Printf(Format, ##__VA_ARGS__))
```
<br/>

# Printing Message To Screen

엄밀히 말하면, 런타임 동안 화면에 출력하는 것은 파일에 저장되지 않으며 따라서 로깅으로 간주하지 않습니다. 하지만, 개발을 할 때 별도의 로그 창을 열지 않고도 게임 창에서 메시지를 볼 수 있으니 훨씬 편리합니다.

```cpp
GEngine->AddOnScreenDebugMessage(-1, 5.f, FColor::White, TEXT("This message will appear on the screen!"));
```

첫 번째 인자는 Key 입니다. 이 키 값이 -1로 셋팅되어 있다면 코드가 실행될 때 마다 새 메시지가 화면에 추가됩니다. 예를 들어 `Tick() `에서 이 함수를 호출할 경우 화면은 메세지가 계속 화면에 흐르듯이 나오게 됩니다. 만약 이 키 값이 양의 정수라면 (uint64) 메시지는 해당 키를 가진 메시지 위로 덮어쓰게 됩니다.

두 번째 인자는 메시지가 표현될 시간(seconds) 입니다.

세 번재 인자는 메시지의 색상입니다.

네 번째 인자는 출력하고자 하는 메시지입니다. 문자열에 매개변수를 사용하고자 한다면 `FString::Printf()` 를 사용해야 합니다.

```cpp
GEngine->AddOnScreenDebugMessage(-1, 5.f, FColor::Red, FString::Printf(TEXT("Some variable values: x = %f, y = %f"), x, y));
```

----

* https://unrealcommunity.wiki/logging-lgpidy6i
* 이득우의 언리얼 C++ 게임개발의 정석
* C++를 사용한 언리얼 엔진 4 개발