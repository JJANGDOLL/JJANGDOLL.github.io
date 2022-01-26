---
title: "Sagers"
excerpt: "작업 중..."

sidebar:
  - title: "Goal"
    text: "언리얼 엔진 4에서 CPP를 사용한 포트폴리오 게임 개발"
  - title: "Genre"
    text: "AOS (수틀리면 언제든 바뀔 수 있음. 그리고 바뀔 확률 높음)"

classes: wide
---

<style type="text/css">
  ul {
    font-size: 13pt; 
  }
</style>

넘쳐나는 파라곤 에셋들로 해본 적도 없는 파라곤이라는 게임을 만들어보자.

사진이 없어서 너무 밋밋하다.

# Minion

<font size="4">양 팀에서 주기적으로 소환되어 적진으로 뛰어가면서 싸우는 작고 귀여운 유닛</font>

## 공통

### Pawn

* AMinionBase <= APawn
  * 아래 Melee, Range, Super의 부모 클래스
  * 자식들에 해당하는 데이터는 DataTable에서 관리됨
  * 생성자에서 DataTable 에서 불러온 값을 토대로 Mesh, AnimInstance, Team ,CapsuleSize 등을 설정
                     

### Controller

* AMinionControllerBase <= AAIController
  * 미니언들의 영혼
  * ActorComponents
    * UAIPerceptionComponent
    * UMinionStateComopnent
    * UMinionLineTraceComponent
    * UMinionAttackComponent
  * 미니언들의 현재 상태와 감지(감각)를 주로 정의

* UAIPerceptionComponent <= UActorComponent
  * 감각기관
  * 시각이 추가되어 있음 (UAISenseConfig_Sight)
  * TeamID 부여와 bDetectEnemies 만 활성화 되어 있어 오로지 적 미니언만 탐지

* UMinionStateComopnent <= UActorComponent
  * 미니언의 현재 상태(Idle, Attack ...)를 정의하는 액터 컴포넌트
  * 20222601 기준 현재 행동 양식은 3가지
    * Idle
    * LineTrace
    * Attack
  * Controller 에서 조건에 따라 단순히 상태를 설정해주는 역할만 존재

* UMinionLineTraceComponent <= UActorComponent, IMinionStateAction
  * 본인 Controller 의 이동 명령을 내리는 액터 컴포넌트
  * 바닥에 따라 그려진 Spline 을 따라 이동
  * 팀에 따라 정방향, 역방향으로 달린다

* UMinionAttackComponent <= UActorComponent, IMinionStateAction
  * 본인 Controller 의 공격 명령을 내리는 액터 컴포넌트
  * AIController 의 Perception 의 감지 여부에 의해 트리거됨

* IMinionStateAction
  * UBTNodeTask 에서 각 컴포넌트의 기능(LineTrace, Attack)이 구현되어 있음을 보장하는 인터페이스

### BehaviorTree

## Melee

## Range

## Super
