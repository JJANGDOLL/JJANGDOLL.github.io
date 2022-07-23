---
title: "Snake"
excerpt: "CMD, CPP 기반 Snake 게임"
url: /portfolio/
header:
  image: /assets/images/snake/header.jpg
  teaser: /assets/images/snake/teaser.jpg
sidebar:
  - title: "Goal"
    text: "Snake 게임 제작"
---

CPP만을 사용한 Snake 게임 제작

게임 프로그램이 패턴, 디자인 패턴에서 학습한 패턴을 사용하는 것이 목표

<br/>

# 실행 화면

![snake_gif](/assets/images/snake/snake.gif)

<br/>

# 구조

각 클래스에 대해서 설명합니다.

<br/>

## 인터페이스

### IUpdate

### IWorld

<br/>

## 클래스

### World

**역할**

1. 게임에 필요한 인스턴스(Snake, Feed ...)들 관리
2. 타이머, 점수 체크등 게임 내/외적 데이터 표시
3. 게임 영역(맵)
4. 게임 시작, 게임 오버 등 게임의 흐름 제어

<br/>

**패턴**

* 팩토리 패턴

```cpp
template<typename T>
T* World::createUpdateObject()
{
    T* newObj = new T();
    newObj->setWorld(this);
    _updateObjects.emplace_back(newObj);
    return newObj;
}
```

<br/>

###



## 팩토리 패턴

