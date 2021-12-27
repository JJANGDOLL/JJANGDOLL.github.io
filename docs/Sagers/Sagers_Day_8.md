---
layout: default
title: "Sagers-Day-8"
nav_order: 8
parent: "Sagers 개발 일지"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 작업 내역

{% include youtubeplayer.html id="vm57x4e7ifQ" %}

# 서로 계속 붙기만 한다

서로를 인식하고 공격을 하는건 했었는데, 인식범위 끝자락에서 허공에 칼만 휘둘러서 우선 붙은 다음에 공격하도록 코드를 짯다<br/>

근데 이젠 서로 근접하는 Task 가 안끝나서 계속 달리고 앉아있다.<br/>

```cpp
void UBTT_AttackMove::TickTask(UBehaviorTreeComponent& OwnerComp, uint8* NodeMemory, float DeltaSeconds)
{
	Super::TickTask(OwnerComp, NodeMemory, DeltaSeconds);

	EPathFollowingRequestResult::Type type = MinionController->MoveToLocation(TargetEnemy->GetActorLocation(), AttackRange, false);
	LogWriter::Log(type);
	if(type == EPathFollowingRequestResult::Failed)
	{
		FinishLatentAbort(OwnerComp);
		return;
	}

	if(type == EPathFollowingRequestResult::AlreadyAtGoal)
	{
		FinishLatentTask(OwnerComp, EBTNodeResult::Succeeded);
		return;
	}
}
```

대체 뭐가 잘못된거지?<br/>

