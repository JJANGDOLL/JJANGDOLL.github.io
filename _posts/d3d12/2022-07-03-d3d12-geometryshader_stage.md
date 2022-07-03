---
title: "D3D 기하 셰이더 단계"

categories:
  - D3D12


tags:
  - d3d12
  - geometry shader stage

toc: true
toc_label: Geometry Shader Stage
toc_sticky: true

sidebar_main: true
---

기하 셰이더 단계(geometry shader)는 선택적인 스테이지로 하나의 온전한 기본도형을 입력받아서 그것을 임의로 변경한다. 예를 들어 삼각형 목록을 그리는 경우 정점 셰이더를 거친 정점 세 개가 기하 셰이더에 입력된다. 여기서 기하 셰이더의 주된 장점은 기하구조를 GPU에서 생성하거나 파괴할 수 있다. 예를 들어 입력 기하구조를 여러 개의 기하구조들로 확장할 수 있고, 조건에 따라 입력 기하구조를 폐기할 수 있다. 기하 셰이더의 흔한 용도는 점이나 선분을 사각형으로 확장하는 것이다. 추가로 기하 셰이더의 출력을 스트림 출력 단계를 통해 메모리의 버퍼에 저장해두고 나종에 활용하는 것이 가능하다.

> 기하 셰이더에서 출력된 정점 위치들은 반드시 hemogeneous clip 으로 변환된 것이여야 한다.