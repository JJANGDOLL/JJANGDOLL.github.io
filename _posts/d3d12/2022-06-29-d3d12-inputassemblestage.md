---
title: "D3D 입력 조립기 단계"

categories:
  - D3D12


tags:
  - d3d12
  - input assemble

toc: true
toc_label: Input Assemble Stage
toc_sticky: true

sidebar_main: true
---

입력 조립기 단계는 메모리에서 기하자료(정점들과 색인들)을 읽어서 기하학적 기본 도형(primitive: 삼각형, 선분, 점 등, 더 복잡한 형태를 만드는 데 사용할 수 있는 기본적인 도형)을 조립한다.

<br/>

# 정점

수학에서 삼각형의 정점(vertex)는 두 변이 만나는 점이고, 선분의 정점은 선분의 양 끝점이고, 점은 그냥 정점이다. 

![5_5_1_1](/assets/images/d3d12/5_5_1_1.png)

D3D에서 정점은 기하 도형을 특징짓는 고유한 점이라는 것 이상으로 다른 정보들도 담을 수 있다. 그덕에 훨씬 더 복잡한 렌더링 효과의 구현이 가능해진다. 정점에 법선 벡터를 추가해서 조명을 구현하고, 텍스쳐 적용을 위해 텍스쳐 좌표를 추가할 수도 있다. D3D는 응용 프로그램이 자신만의 정점 형식을 정의할 수 있는 유연성을 제공한다.

<br/>

# Primitive Topology

정점들은 정점 버퍼(vertex buffer)라고 하는 특별한 D3D 자료구조 안에 담겨서 렌더링 파이프라인에 묶인다. 정점 버퍼는 정점들을 연속적인 메모리에 저장하는 자료구조이다. 정점 버퍼 자체에는 기본 도형을 형성하기 위해 정점들을 조합하는 방법에 관한 정보가 없다. 예를 들어 두 정점을 써서 선을 그어야 하는지 세 정점을 써서 삼각형을 그려야 하는지 등 말이다. 정점 자료를 이용해서 기하학적 기본 도형을 형성하는 방법은 primitive topology[기본도형 위상구조](https://docs.microsoft.com/ko-KR/windows/win32/api/d3dcommon/ne-d3dcommon-d3d_primitive_topology) 라는 것을 사용해야 한다.

```cpp
void IASetPrimitiveTopology(
  [in] D3D11_PRIMITIVE_TOPOLOGY Topology
);

typedef enum D3D_PRIMITIVE_TOPOLOGY {
  D3D_PRIMITIVE_TOPOLOGY_UNDEFINED = 0,
  D3D_PRIMITIVE_TOPOLOGY_POINTLIST = 1,
  D3D_PRIMITIVE_TOPOLOGY_LINELIST = 2,
  //...
} ;
```

이후 모든 그리기 호출은 현재 설정된 primitive topology 를 사용한다.

```cpp
mCommandList->IASetPrimitiveTopology(
 D3D_PRIMITIVE_TOPOLOGY_LINELIST);
/* ...draw objects using line list... */
mCommandList->IASetPrimitiveTopology(
 D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
/* ...draw objects using triangle list... */
mCommandList->IASetPrimitiveTopology(
 D3D_PRIMITIVE_TOPOLOGY_TRIANGLESTRIP);
/* ...draw objects using triangle strip... */
```

여기선 대부분 삼각형 목록을 사용한다.

<br/>

## 점 목록

D3D_PRIMITIVE_TOPOLOGY_POINTLIST 를 지정해서 IASetPrimitiveTopology 를 호출하면 점 목록이 현재 위상구조로 설정된다. 

![5_5_2_1_1](/assets/images/d3d12/5_5_2_1_1.png)

<br/>

## 선 띠

D3D_PRIMITIVE_TOPOLOGY_LINESTRIP 을 지정하면 선 띠(Line Strip) 또는 선분 띠가 설정된다.

![5_5_2_1_2](/assets/images/d3d12/5_5_2_1_2.png)

<br/>

## 선 목록

D3D_PRIMITIVE_TOPOLOGY_LINELIST 를 지정하면 선 목록이 설정된다. 선 목록이 설정된 상태에서 그리기 호출의 매 정점 두 개가 하나의 선분을 형성한다.

![5_5_2_1_3](/assets/images/d3d12/5_5_2_1_3.png)

<br/>

## 삼각형 띠

D3D_PRIMITIVE_TOPOLOGY_TRAINGLESTRIP 을 사용하면 삼각형 띠가 설정된다.

![5_5_2_1_4](/assets/images/d3d12/5_5_2_1_4.png)

삼각형 띠에서 짝수 번째 삼각형과 홀수 번째 삼각형이 감기는 순서가 다르다는 점을 주목하자. 이 때문에 후면 선별시 문제가 발생한다. 이것을 위해 GPU는 내부적으로 짝수 번째 삼각형의 처음 두 정점의 순서를 맞바꾸어서 홀수번째 삼각형과 같은 순서가 되게 만든다.

![5_5_2_1_5](/assets/images/d3d12/5_5_2_1_5.png)

<br/>

## 삼각형 목록

D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST 를 지정하면 삼각형 목록이 설정된다. 그리기 호출의 매 정점 세 개가 하나의 삼각형을 형성한다. 삼각형 띠와 달리 따로 떨어진 삼각형을 형성할 수 있다.

![5_5_2_1_5](/assets/images/d3d12/5_5_2_1_6.png)

<br/>

## 인접성 정보를 가진 기본 도형

삼각형 목록을 만들 때, 각 삼각형에 그에 접한 이웃 삼각형 세 개에 관한 정보를 포함할 수 있다. 그렇게 만든 삼각형 목록을 "adjacency(인접성) 정보를 가진 삼각형 목록"이라고 부르고, 주어진 삼각형에 접한 삼각형을 adjacent triangle(인접 삼각형)이라고 부른다.

![5_5_2_1_7](/assets/images/d3d12/5_5_2_1_7.png)

이러한 삼각형 목록은 인접 삼각형들에 접근해야 하는 특정한 기하 셰이딩 알고리즘을 기하 셰이더에서 구현할 때 쓰인다. 기하 셰이다거 그런 인접 삼각형에 접근하려면 삼각형 자체와 함꼐 인접 삼각형들의 정보도 정점 버퍼와 색인 버퍼에 담아서 파이프라인에 제출해야 한다. 또한, 반드시 위상구조를 D3D_PRIMITIVE_TOPOLOGY_TRIANGLELIST_ADJ 로 지정해야 한다. 그래야 파이프라인이 정점 버퍼로부터 삼각형과 그 인접 삼각형을 구축할 수 있다. primitive adjacent 는 오직 기하 셰이더의 입력으로만 쓰일 뿐 실제로 그려지는 것은 아니다.

<br/>

## 제어점 패치 목록

D3D_PRIMITIVE_TOPOLOGY_N_CONTROL_POINT_PATCHLIST 위상구조는 정점 자료를 N개의 제어점(control point) 들로 이루어진 패치 목록으로 해석해야 함을 뜻한다. 이 제어점은 렌더링 파이프라인의 테셀러레이션 단계들에 쓰인다.

<br/>

# 색인

3D 물체의 기본 요소는 삼각형이다. 예를 들어, 삼각형 하나와 팔각형 하나를 삼각형 목록을 이용해서 만들 때, 이에 필요한 정점 배열들이다.

```cpp
Vertex quad[6] = {
 v0, v1,v2, // Triangle 0
 v0, v2, v3, // Triangle 1
};
Vertex octagon[24] = {
 v0, v1, v2, // Triangle 0
 v0, v2, v3, // Triangle 1
 v0, v3, v4, // Triangle 2
 v0, v4, v5, // Triangle 3
 v0, v5, v6, // Triangle 4
 v0, v6, v7, // Triangle 5
 v0, v7, v8, // Triangle 6
 v0, v8, v1 // Triangle 7
};
```

![5_5_3_1](/assets/images/d3d12/5_5_3_1.png)

삼각형 정점들을 지정하는 순서가 중요한데, 이를 winding order(감기 순서)라고 부른다.

위에서 보듯 하나의 3차원 물체를 형성하는 삼각형들은 여러 개의 정점을 공유한다. 구체적으로는 사각형에서 $V_0, V_2$ 를 공유한다. 그래서 배열에는 이 정점이 두번 들어있는 것을 주목하자. 근데 이렇게 정점이 중복되면 바람직지 않은 이유가 있다.

1. 메모리 요구량의 증가
2. 그래픽 하드웨어의 처리량 증가

삼각형 띠 모양을 쓴다면 중복 정점 무넺가 완화되지만, 모형의 기하구조를 삼각형 띠 형태로 구성하는 일이 항상 가능한건 아니다. 따라서, 이 문제를 해결하는 방법은 색인(index)를 이용하는 것이다. 색인을 이용하는 정점 목록과 함께 색인 목록을 하나 만든다. 그래서 정점 목록은 모두 고유한 정점들로 이루어지고, 색인 목록은 어떤 정점들을 어떤 순서를 사용해서 삼각형을 형성해야 하는지를 나타내는 색인으로 이루어진다. 돌아가서, 위 예시라면 사각형은 정점 4개, 팔각형은 정점 9개면 충분하다.

사각형을 예시로 든다면

```cpp
Vertex v[4] = {v0, v1, v2, v3};

UINT indexList[6] =
 {
  0, 1, 2,  // 삼각형 0
  0, 2, 3   // 삼각형 1
 }
```

인덱스 리스트의 의미는 $V_0, V_1, V_2$ 를 써서 삼각형 하나를 만들고 $V_0, V_2, V_3$ 을 써서 삼각형 하나를 만들라는 얘기이다.

그래픽 카드는 정점 목록의 고유한 정점들을 처리한 후, 색인 목록을 이용해서 정점들을 조합해 삼각형을 형성한다. 정점 목록의 '중복'이 색인 목록으로 옮겨간 셈인데, 별로 문제가 되지 않는다.

1. 색인은 그냥 정수닌까 정점 구조체보다 메모리를 적게 머근다.
2. 정점들이 적절한 순서로 캐시에 저장된다면, 그래픽 하드웨어는 중복된 정점들을 (너무 자주) 처리할 필요가 없어진다.
