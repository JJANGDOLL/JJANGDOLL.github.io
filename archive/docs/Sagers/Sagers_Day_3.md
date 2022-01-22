---
layout: default
title: "Sagers-Day-3"
nav_order: 2
parent: "Sagers 개발 일지"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 작업 내역

## 언리얼 코딩 규칙 정리

[UnrealCodingStandard](https://docs.unrealengine.com/4.27/ko/ProductionPipelines/DevelopmentSetup/CodingStandard/)

* public 이후 private
* 첫 줄에는 주석으로 저작권 공지
* 첫 글자 대문자
* 단어 사이에 공백을띄우지는 않음
* 대문자 한 글자로 접두사를 붙임
  * 템플릿 클래스 접두사 T
  * UObject 상속 접두사 U
  * AActor 상속 접두사 A
  * SWidget 상속 접두사 S
  * 추상 인터페이스 접두사 I
  * 열거형 접두사 E
  * Boolean 접두사 b
  * Typedef 접두사는 적절히 붙인다.
  * C# 에서는 접두사 생략
* 변수, 함수, 클래스 이름은 명확하고 서술적으로
* void 반환 함수는 동사 뒤에 이름을 명명
* 입력받는 인자는 In 접두사를, 출력하는 인자는 Out 접두사를
* 인자가 Boolean 인 경우 bIn, bOut 접두사를
* 값을 반환하는 함수의 경우 반환 값에 대한 예측이 가능해야 한다.
  * bool CheckTea() => bool IsTeaFresh()
* 주석이 없어도 설명이 가능한 코드로 작성
* const 를 적절히 사용
* override 와 final  강력 권장
* null => nullptr
* auto는 가급적 자제
* 람다 함수는 문서화 하자
* Blueprint 에 노출하기 위한 enum 은 uint8 기반
* 대괄호 : 새 줄에 대괄호를 넣는게 관행. 단일 문장 블록도 대괄호 포함.
* if, else : 실행 블록은 대괄호로 묶음.
* 들여쓰기 : 탭, 블록별로 코드 들여쓰기. 줄 시작부분은 탭을 사용하지만, 줄 맞추기 위해선 스페이스를 쓸 수도 있음. C# 이라면 반드시 탭
* Switch : default case 는 기본, 각각의 케이스로 넘어가는 것을 구체적으로 명시
* 파일이름에는 접두사를 붙이지 않는 편이 좋다.
* 모든 헤더엔 #pragma once
* 가능하다면 헤더 포함 대신 전방 선언으로 대체
* include 는 가급적 세분화해서 
* 간접적으로 include 되는 헤더에 대해서 의존하지 않음
* 폴더는 private, public 구분. 다른 모듈에서 필요로 하는 정의는 public 에서 정의
* 인라인 함수는 남용하지 말자
* 가장 마지막으로 상속받는 class 에는 final 을 사용
* 스트링 리터럴에는 TEXT() 매크로 사용
* 포인터와 레퍼런스 공백은 오른쪽에 딱 한칸
  * FShaderType* Ptr
* 파라미터가 너무 긴 경우 구조체를 고려
* bool, FString 함수 오버로드는 피한다

<br/>

## Utility 기능 추가

### LogWriter 클래스

기능 : 화면, 로그에 다양한 형태의 값 출력

### ObjectHelper 클래스

기능 : 에셋 로드, 언리얼 객체 생성, 언리얼 클래스 가져오기 등 객체관련 도움 함수

### ActorGizmo 클래스

기능 : 액터의 X,Y,Z 축을 보여줌

### GizmoComponent 클래스

기능 : 씬 컴포넌트 상속으로 bool 값에 따라 X,Y,Z 축을 보여줌

<img src="/images/Unreal/Sagers/utilities_gizmo_1.PNG" width="300px">
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="/images/Unreal/Sagers/utilities_gizmo_2.PNG" width="300px">
