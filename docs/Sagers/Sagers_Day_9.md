---
layout: default
title: "Sagers-Day-9"
nav_order: 9
parent: "Sagers 개발 일지"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 처음부터 다시하는 중..

수업이나 블프때 했던 내용들 중에 행동 상태(State)를 쌈박하게 잘 정리해보고 싶었고, 그렇게 생각해낸게 <br/>

**행동을 한 후에 상태 변화를 호출** 하는 방식이였다.<br/>

이러면 이미 행동을 하고있으니 상태가 꼬일 일이 없을꺼라고 생각했다.<br/>

근데 이렇게 짜며 맨 처음에 닥친 문제가 **행동은 언제 트리거해야하지** 였다.<br/>

여태껏 상태가 할 행동을 호출해줬는데 거꾸로 실행을 해야하니....<br/>

그래서 어거지로 일단 달린다는 기본 행동을 실행시켰다. 지금 생각해보면 이때 그냥 멈췄어야 했음....<br/>

이후로 달리고, 공격하고, Idle 하는 상태를 추가할 수록 호출 구조가 점점 산으로 갔다. <br/>

기본 행동인 Run Task 가 실행되면 Run->StateChange를 호출하고, Perception 도 감지하면 State->Attack->StateChange 를 호출하고 또 Attack 조건이 끝나면 StateChange->Run->StateChange 호출하고 아주 정신이 없다.<br/>

나조차도 호출구조가 어떤지 감도 안잡혀서 손을 놔버렸다. 이 망한 구조는 더 쌓아올릴수가 없음<br/>

결국 처음부터 다시해야 됬는데 만든거 또 만들려니 의욕이 안나는데...<br/>

깃 로그보면 10일정도 논거같은데 이젠 다시 해야할 듯<br/>

<img src="/images/Unreal/Sagers/sagers_9_1.PNG" width = 700px><br/>

놀면서 잘한게 있다면 알고리즘을 틈틈히 공부하고 있다는거정도... 백준 실3 찍었다.<br/>

<br/>

# 미니언 종류 추가

<img src="/images/Unreal/Sagers/sagers_9_2.PNG" width = 700px><br/>

미니언 타입 3종류, 양팀 미니언 부터 추가하면서 다시 시작<br/>

<br/>

# 미니언 행동트리 추가중

그냥 멀쩡한 구조로 다시 짜고있는중...<br/>

<br/>