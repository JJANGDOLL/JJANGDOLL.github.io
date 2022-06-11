---
title: "모양의 표현"

categories:
  - D3D12


tags:
  - d3d12
  - model presentation

toc: true
toc_label: MODEL PRESENTATION
toc_sticky: true

sidebar_main: true
---

이랍ㄴ적으로 D3D application 에서는 물체를 삼각형으로 근사해서 표현한다. 따라서, 우리가 컴퓨터 세상에서 만드는 모형의 기본적인 요소는 삼각형입니다. 실제로 세상의 그 어떤 물체도 삼각형으로 근사하게 표현할 수 있습니다. 삼각형을 더 많이 사용할 수록 더 자세하게 묘사가 가능합니다. 하지만, 삼각형이 많기 때문에 필요한 처리량도 덩달아 늘어납니다. 따라서, 응용프로그램 개발자는 대상 사용자의 하드웨어 성능에 적절히 맞추어 균형을 이루어야 합니다.

![3d_model](/assets/images/d3d12/3d_model.png)

위 모형에는 수많은 삼각형이 쓰였습니다. 이처럼 모형을 3d로 일일이 조각하는건 정말 성가신일입니다. 3D 물체의 모형을 생성하고 조작할 때는 3D 모델러라고 하는 3차원 모형 제작 응용 프로그램을 사용합니다. 흔히 3D Studio MAX, LightWave 3D, Maya, Blender 들이 있습니다.




