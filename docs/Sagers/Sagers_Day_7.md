---
layout: default
title: "Sagers-Day-7"
nav_order: 7
parent: "Sagers 개발 일지"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 작업 내역

{% include youtubeplayer.html id="a1lQKk8VlTU" %}

## AI는 이제 서로를 적으로 인식합니다

이제 AI는 팀으로 그룹이 되어있으며 서로 적으로 인식할 수 있습니다.<br/>

### 왜 TeamID가 다른데 AI끼리 인식을 못했을까?

* 문제 : PerceptionUpdate 함수가 호출되지 않음<br/>

1. OnPerceptionUpdated 에 바인딩이 잘 되었는가?<br/>

디버깅 걸고 갯수 확인해보니 1개로 잘 나온다.<br/>

2. PerceptionUpdated 가 진짜 호출이 안되는 건가?<br/>

간단한 로그 남기니 정말로 호출이 안됨<br/>

3. 서로를 Hostile 로 인식하는가?<br/>

Nutral, Friendly 전부 켜고 테스트 => 인식함<br/>
Nutral 만 켜고 테스트 => 인식함<br/>
Friendly 만 켜고 테스트 => 인식못함<br/>

4. TeamID가 정말 다른가?<br/>

Tick 에서 실시간으로 확인하니 둘은 서로 다른 TeamID를 가지고 있음<br/>

5. 왜 둘은 Nutral로 인식하는가?<br/>

서로 인식하는 원리에 대해서 올라가봄<br/>

```cpp
class AIMODULE_API IGenericTeamAgentInterface
{
	...

	/** Retrieve team identifier in form of FGenericTeamId */
	virtual FGenericTeamId GetGenericTeamId() const { return FGenericTeamId::NoTeam; }

	/** Retrieved owner attitude toward given Other object */
	virtual ETeamAttitude::Type GetTeamAttitudeTowards(const AActor& Other) const
	{ 
		const IGenericTeamAgentInterface* OtherTeamAgent = Cast<const IGenericTeamAgentInterface>(&Other);
		return OtherTeamAgent ? FGenericTeamId::GetAttitude(GetGenericTeamId(), OtherTeamAgent->GetGenericTeamId())
			: ETeamAttitude::Neutral;
	}
};
```
SetGenericTeamID 에서 TeamID를 설정해주는데 GetGenericTeamID 는 NoTeam(255) 중립을 리턴하고 있다. <br/>
근데 AAIController 에서 GetGenericTeamID 를 아래처럼 재정의했다.<br/>

```cpp
	virtual FGenericTeamId GetGenericTeamId() const override { return TeamID; }
```

그러닌까 GetGenericTeamID 문제는 아니였다. 근데 그 아래있는 GetTeamAttitudeTowards 는 뭘까?<br/>

BP 거닌까 걸리고, 결론을 찾음.<br/>

<img src="/images/Unreal/Sagers/sagers_7_1.PNG"><br/>

GetAttidueTowards 는 두 액터 사이의 관계를 반환해주는 함수다. 그러닌까 두 액터가 다른 TeamID 라면 Hostile 을 반환해준다.<br/>

근데 왜 제대로 동작을 안 했을까?<br/>

인자를 살펴보면 AActor 형태의 값이 들어오는데, 이 값은 컨트롤러가 아니라 캐릭터 다.<br/>
GetTeamAttitudeTowards 함수를 보면 IGenericTeamAgentInterface 의 구현이 없으면 ETeamAttitude::Neutral 를 반환해준다.<br/>
IGenericTeamAgentInterface 는 컨트롤러에 구현했지 캐릭터에는 구현이 없다. 그러닌까 계속 중립만 반환 했던 것.<br/>

* 해결 1. 캐릭터에서 IGenericTeamAgentInterface 를 구현한다.
* 해결 2. AActor 인자에서 Controller 를 가져온다.



<br/><br/>

## TeamID 버그

6일차에서 나타났던 버그. TeamID가 Construction 에서 설정 했음에도 불구하고 계속 0이됨 <br/>

* 원인 : OnConstruction => LoadMap 사이 어딘가에서 언리얼에서 0으로 초기화해버림. 자세한 조사는 못했지만 해당 값을 디버깅해보니 어느 순간 0으로 다시 바뀌는 것을 확인<br/>

* 해결 : UPROPRTY 매크로 추가<br/>