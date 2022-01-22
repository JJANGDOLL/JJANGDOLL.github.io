---
layout: default
title: "Sagers-Day-11"
nav_order: 11
parent: "Sagers 개발 일지"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# UBTService 착각

UBTService 는 어디서 어떤식으로 인스턴스 되는걸까...?<br/>

전일차 글 올린거 보면 TickNode 에서 계속 캐스팅 하는 작업을 줄이려고 클래스 멤버를 선언하고 SearchStart 에서 값을 넣어줬는데...<br/>

이후 TickNode 에서 Blackboard 값이 안바뀌는 이상한 현상이 생겻다.<br/>

**아래 코드의 문제점이 뭘까?**

```cpp
UCLASS()
class SAGERS_API UBTSMinionState : public UBTService
{
	GENERATED_BODY()
	
public:
	UBTSMinionState();

protected:
	virtual void OnSearchStart(FBehaviorTreeSearchData& SearchData) override;
	virtual void TickNode(UBehaviorTreeComponent& OwnerComp, uint8* NodeMemory, float DeltaSeconds) override;

private:
	AController* MinionController;
};

---------------------------------
void UBTSMinionState::OnSearchStart(FBehaviorTreeSearchData& SearchData)
{
    Super::OnSearchStart(SearchData);

    PrintLine();
    
    MinionController = SearchData.OwnerComp.GetOwner()->GetInstigatorController();
}

void UBTSMinionState::TickNode(UBehaviorTreeComponent& OwnerComp, uint8* NodeMemory, float DeltaSeconds)
{
    Super::TickNode(OwnerComp, NodeMemory, DeltaSeconds);

    //MinionController Do Somting
    ~~~
}
```

위처럼 작성하고 TickNode 에서 MinionController 로 무언가를 하면 딱 한놈만 바뀐다.<br/>

그 이유는 UBTService 인 UBTMinionState 는 몬스터당 하나씩 가지고 있는 구조가 아니였다. 어딘가에서 한번만 인스턴스 되는 듯.<br/>

그래서 클래스 멤버 변수인 MinionController 가 OnSearchStart 가 호출되면서 계속 값이 바뀌고, 그 MinionController 에서 뭘 하면 딱 한 액터에서만 동작을 하더라. 물론 그 대상이 내가 원하는 특정 컨트롤러인지는 보장할 수 없다.<br/>

더 찾아보는 것도 재밌긴 할텐데 나중에 하기로<br/>
