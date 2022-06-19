---
title: "D3D 렌더링 파이프라인 개요"

categories:
  - D3D12


tags:
  - d3d12
  - rendering pipeline

toc: true
toc_label: Rendering Pipeline
toc_sticky: true

sidebar_main: true
---

렌더링 파이프라인은 3차원 장면의 기하학적 서술과 가상 카메라의 위치 및 방향이 주어졌을 때 현재 가상 카메라에 미친 3차원 장면의 모습에 근거해서 2차원 이미지를 생성하는 데 필요한 일련의 단계들 전체를 가르키는 용어이다. 

화살표는 그 단계가 GPU 자원에 자료를 기록할 수 있음을 뜻한다. 대부분의 단계는 GPU에 자료를 기록하지 않는다.

![rp](/assets/images/d3d12/rp.png)


