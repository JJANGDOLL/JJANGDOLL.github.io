---
title: "D3D 픽셀 셰이더 단계"

categories:
  - D3D12


tags:
  - d3d12
  - pixel shader

toc: true
toc_label: Pixel Shader Stage
toc_sticky: true

sidebar_main: true
---

픽셀 셰이더는 프로그래머가 작성하고 GPU가 실행하는 프로그램이다. 픽셀 셰이더는 각각의 픽셀 단편에 대해 실행된다. 기본적으로 픽셀 셰이더는 보간된 정점 특성들을 입력받아서 하나의 색상을 출력한다. 픽셀 셰이더는 그냥 고정된 상수 색상을 돌려주는 간단한 것 부터, 픽셀당 조명, 반사, 그림자 효과 등 복잡한 작업을 까지 다양하게 수행한다.