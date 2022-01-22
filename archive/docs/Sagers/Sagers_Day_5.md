---
layout: default
title: "Sagers-Day-5"
nav_order: 5
parent: "Sagers 개발 일지"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 작업 내역

{% include youtubeplayer.html id="pJn1wf2U9Mc" %}

## 경로 이동 컴포넌트

* Spline 따라서 움직이는 UPatorlComponent 추가

## Minion 애니메이션

* 이동 시에 달리는 애니메이션

<img src="/images/Unreal/Sagers/sagers_5_1.PNG">

## 구조상의 큰 문제

구조를 좀 바꿔봤는데 망한것같다.<br/>
원래라면 State::SetRunMode => Behavior::SetRunMode 이런식이였는데 (달리는상태 => 달리기)<br/>

Component::Run => State::SetRunMode => Behavior:SetRunMode 이런식으로 (달리면=>달리는상태)<br/>
로 바꾸고, 행위가 상태를 정의하도록 바꿔봤다. <br/>

여기서 큰 문제가 생긴게, 행위의 트리거 조건을 모르겠다.<br/>

즉, 달리고 있으면 달리는 상태로 업데이트 해주는 구조인데 문제는 **언제 달리는지?** 를 언제 호출해야 할지가 문제...<br/>



