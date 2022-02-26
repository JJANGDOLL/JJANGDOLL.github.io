---
title: "UE4 로깅(UE_LOG)"
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

# Formatting Example Quick Reference

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

----

https://unrealcommunity.wiki/logging-lgpidy6i
