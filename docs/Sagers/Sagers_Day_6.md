---
layout: default
title: "Sagers-Day-6"
nav_order: 6
parent: "Sagers 개발 일지"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 작업 내역



## BehaviorTree

어제 고민했던 기본 이벤트 트리거 조건.<br/>

그냥 달리기를 무저껀 실행하게 하고, 거기서 실행되는 이벤트가 있으면 알아서 찾아가도록 하는걸로 함<br/>

<img src="/images/Unreal/Sagers/sagers_6_1.PNG">

그러면 무저껀 달려가는 건 실행된다.

<br/>

## TeamID 버그

Character 의 데이터(매시, 애님클래스, TeamID) 값을 전부 Construction 에서 가져오고 적용한다.<br/>

근데 AIController Perception 에서 TeamID 를 가져오면 전부 0이다.<br/>

```cpp
UCLASS()
class SAGERS_API AMinionBase: public ACharacter
{
	GENERATED_BODY()

public:
	AMinionBase();

	FORCEINLINE uint8 GetTeamID() { LogWriter::Log(this); return TeamId; }

private:
	uint8 TeamId;

};

```

```cpp
void AMinionControllerBase::OnPossess(APawn* InPawn)
{
	Super::OnPossess(InPawn);

	ControlledPawn = Cast<AMinionBase>(InPawn);
	ControlledPawn->SetMinionController(this);
	UBehaviorTree* BehaviorTree = ControlledPawn->GetMinionBehaviorTree();
	if(!BehaviorTree)
	{
		LogWriter::Print(GetName()+L" BehaviorTree Not Set");
		return;
	}
	UseBlackboard(BehaviorTree->BlackboardAsset, Blackboard);


	LogWriter::Log(TEXT("TEAM ID"));
	LogWriter::Log(ControlledPawn->GetTeamID());
	SetGenericTeamId(ControlledPawn->GetTeamID());
	Perception->OnPerceptionUpdated.AddDynamic(this, &AMinionControllerBase::PerceptionUpdate);


}
```

ControllerBase 의 GetTeamID 를 직어보면 전부 0이나온다. 대체 왜그럴까;;;