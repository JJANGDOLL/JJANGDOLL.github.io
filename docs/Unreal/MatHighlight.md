---
layout: default
title: "Highlight System"
nav_order: 1
parent: "UnrealEngine"
---

# Highlight Material
{: .no_toc}

## 머터리얼 다운로드
{: .no_toc}

특정 물체를 강조하는 머터리얼

사용 버전 : 4.22

<img src="/images/Unreal/mat_highlight_2.PNG">

[매터리얼다운로드](/files/unreal/assets/MAT_Highlight.uasset)

---

# 액터 테투리를 하이라이트 하는 방법

## Modes 에서 PostProcessVolume 을 월드에 배치한다.

<img src="/images/Unreal/highlight/image1.png">

&nbsp;<br/>

## Post Process Volume 사이즈를 정한다.

포스트 프로세스 볼륨 크기를 하이라이트할 액터가 들어갈 만큼 넉넉한 사이즈로 정한다.

만약, 사이즈에 구애받지 않으려면 Infinite Extend (Unbound) 설정을 체크한다.

<img src="/images/Unreal/highlight/image2.png">

&nbsp;<br/>

## Post Process Volume 의 머터리얼을 지정한다.

머터리얼은 위에 업로드된 머터리얼이다.

<img src="/images/Unreal/highlight/image3.png">

&nbsp;<br/>

## 대상 액터의 렌더링 설정을 변경한다.

StaticMesh 의 디테일 -> Render -> Render CustomDepth Pass 활성화

<img src="/images/Unreal/highlight/image4.png">

&nbsp;<br/>

## 결과 확인

<img src="/images/Unreal/highlight/image5.png">

