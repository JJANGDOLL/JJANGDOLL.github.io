---
title: "D3D 출력 병합기 단계"

categories:
  - D3D12


tags:
  - d3d12
  - output merger

toc: true
toc_label: Output Merger
toc_sticky: true

sidebar_main: true
---

픽셀 셰이더가 생성한 픽셀 단편들은 렌더링 파이프라인의 output merger(OM) 단계로 입력된다. 이 단계에서 일부 픽셀 단편들이 탈락될 수 있다(깊이 판정이나 스텐실 판정에 의해). 탈락되지 않은 픽셀 단편들은 후면 버퍼에 기록된다. 혼합도 이 단계에서 일어난다. 혼합이란, 새 픽셀이 후면 버퍼의 기존 픽셀을 완전히 덮어쓰는 것이 아닌, 두 픽셀을 일정한 공식에 따라 섞은 결과를 기록하는 것을 의미한다. 혼합은 반투명 같은 특수 효과를 내는 데 쓰인다.

