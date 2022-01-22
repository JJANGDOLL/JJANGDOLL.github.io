---
layout: default
title: "Sagers-Day-10"
nav_order: 10
parent: "Sagers 개발 일지"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# BTService, BTTask 구성중

OnSearchStart 에서도 이런식으로 컨트롤러를 가져올 수 있었다. <br/>

```cpp
void UBTSMinionState::OnSearchStart(FBehaviorTreeSearchData& SearchData)
{
    Super::OnSearchStart(SearchData);

    PrintLine();
    
    MinionController = SearchData.OwnerComp.GetOwner()->GetInstigatorController();
}
```

```cpp
APawn* AActor::GetInstigator() const
{
	return Instigator;
}

AController* AActor::GetInstigatorController() const
{
	return Instigator ? Instigator->Controller : nullptr;
}
```

OwnerComp.GetOwner() 는 AActor 를 반환하는데 Instigator 라는 애 덕분에 참 쉽게 컨트롤러를 가져온다. **왜 이런게 가능한거지?**<br/>

어쨋든 컨트롤러 가져왔으면 연관된 오브젝트들도 가져올 수 있을테니 다른 객체들에 접근이 쉬워질듯<br/>

언리얼은 잘만들었다.<br/>

<br/>

BTTree 구조는 상태 설정에 노티파이랑 StateComponent 을 사용하고 Service 에서는 상태를 읽어오는 방식으로 다시 하기로...<br/>

<br/>

# AIController 설정에 아무리봐도 이상한 코드

```cpp
AMinionBase::AMinionBase()
{
    ...
    ObjectHelper::GetClass<AController>(&AIControllerClass, "Class'/Script/Sagers.MinionControllerBase'");
    ...
}
```

GetClass 이면 _C 붙여서 클래스를 가져와야하는데 왜 잘 되는거지?<br/>

```cpp
    ObjectHelper::GetClass<AController>(&AIControllerClass, "Class'/Script/Sagers.MinionControllerBase_C'");
```

이상하게 이 코드가 안된다.<br/>

블프 콘텐츠랑 CPP 스크립트에서 GetClass 할떄 뭔가 다른가? 애초에 CPP은 GetClass 할 필요가 없으려나?<br/>