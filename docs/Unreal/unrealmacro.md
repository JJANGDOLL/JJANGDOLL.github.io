---
layout: default
title: "언리얼 오브젝트 처리"
nav_order: 2
parent: "Unreal"
---

# Unreal Object Handling

클래스, 프로퍼티, 함수에 알맞은 매크로(UPROPERTY, UFUNCTION, ...)를 붙이면 언리얼 엔진에서 접근이 가능해지며, 이를 통해 내부의 다양한 처리 기능을 구현할 수 있습니다.

&nbsp;

## Garbage Collection

언리얼은 더이상 참조되지 않거나 명시적으로 제거한 UObject 를 주기적으로 정리합니다. 엔진에서는 레퍼런스 그래프를 만들어 오브젝트의 사용 유무를 판별하고, 필요 없다고 판단한 오브젝트는 제거합니다.
