---
layout: default
title: "Sagers-Day-4"
nav_order: 2
parent: "Sagers 개발 일지"
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 작업 내역

## Minion 데이터 테이블 적용

Minion 들에 대해서 Mesh, AnimBP, HalfHeight, Radius 등 필요한 데이터를 데이터 테이블에서 로드하도록 적용<br/>
이제 DataIndex에 따라 미니언의 종류가 변함

<img src="/images/Unreal/Sagers/sagers_4_1.PNG">

<br/>

<img src="/images/Unreal/Sagers/sagers_4_2.PNG">

<br/>

## APatrolPath

미니언의 경로를 Spline 으로 그려주는 클래스

<img src="/images/Unreal/Sagers/sagers_4_3.PNG">

<br/>

## UPatrolComponent (작업 중)

APatrolPath의 경로를 따라서 움직일 수 있게 하는 클래스
직접 움직일 수 있게할지, 목적지 데이터를 전달하는 방식으로 작업할 지 고민중

