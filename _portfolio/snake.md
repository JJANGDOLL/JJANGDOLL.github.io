---
title: "Snake"
excerpt: "CMD, CPP 기반 Snake 게임"
url: /portfolio/
header:
  image: /assets/images/snake/header.jpg
  teaser: /assets/images/snake/teaser.jpg
sidebar:
  - title: "주제"
    text: "Snake 게임 제작"

toc: true
toc_label: Snake
toc_sticky: true    
---

CPP만을 사용한 Snake 게임 제작

게임 프로그램이 패턴, 디자인 패턴에서 학습한 패턴을 사용하는 것이 목표

[Snake Code](https://github.com/JJANGDOLL/Snake)

<br/>

# 실행 화면

![snake_gif](/assets/images/snake/snake.gif)

<br/>

# 클래스별 상세 기능

각 클래스의 역할에 대해서 설명합니다.

<br/>

## Global

### randomInRange

1. 입력받은 숫자 내에서 랜덤한 값을 반환

<br/>

## 인터페이스

### IUpdate

1. 게임 각 객체에서 매 프레임당 실행할 기능
2. 이벤트 수신에 대한 처리 기능
3. 게임 흐름(시작, 종료)에 대한 기능

<br/>

### IWorld

1. 각 클래스가 월드에 대한 참조를 가지는 기능

<br/>

## 클래스

### World 

1. 게임에 필요한 인스턴스(Snake, Feed ...)들 관리
2. 타이머, 점수 체크등 게임 내/외적 데이터 표시
3. 게임 영역(맵)
4. 게임 시작, 게임 오버 등 게임의 흐름 제어

<br/>

### Screen 

1. CMD에 좌표와 어떤 글자를 출력해야할 데이터를 관리
2. 게임 표현해야 할 데이터를 CMD에 출력

<br/>

### Snake 

1. 뱀의 길이, 방향, 위치 등의 정보를 관리

<br/>

### Feed

1. 뱀이 먹을 수 있는 먹이 기능
2. 먹이에 대한 위치를 관리

<br/>

### InputController

1. 게임 입력에 관한 조작을 관리

<br/>

# 구조

## 디자인 패턴

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

* 싱글턴 패턴

```cpp
class World
{
public:
    static World& getInstance(int InSize = 0, Screen* InScreen = nullptr);

private:
    World(const World&);
    World& operator=(const World&);
};
```

```cpp
World& World::getInstance(int InSize /*= 0*/, Screen* InScreen /*= nullptr*/)
{
    static World* _instance;
    if(_instance == nullptr)
    {
        assert((InSize > 0) && (InScreen != nullptr));

        _instance = new World(InSize, InScreen);
        _instance->createMap();
    }
    return *_instance;
}
```

<br/>

## 게임 프로그래밍 패턴

* 게임 루프

```cpp
while(!World::getInstance().isEnd())
{
    start = std::chrono::steady_clock::now();

    std::chrono::duration<double> elapsedTime = std::chrono::steady_clock::now() - start;
    term = (World::getInstance().getPerSecond() - elapsedTime.count());
    if(term > 0)
        Sleep(int(term * 1000));
}
```

<br/>

* 업데이트 메소드

```cpp
InputController* controller = World::getInstance().createUpdateObject<InputController>();
Snake* snake = World::getInstance().createUpdateObject<Snake>();
Feed* feed = World::getInstance().createUpdateObject<Feed>();
```

```cpp
while(!World::getInstance().isEnd())
{
    World::getInstance().updateAll();
}
```

```cpp
void World::updateAll() 
{
    ElapsedTimer();
    printScore();
    renderMap();
    if(!_bGameStart)
    {
        readyToStart();
    }

    for(IUpdate* u : _updateObjects)
    {
        u->update();
    }
}
```
<br/>

* 이중 버퍼

```cpp
class Screen
{
private:
    int currentIdx = 0;
    ScreenBuffer screenBuffer[2];
public:
    ScreenBuffer& GetCurrentBuffer();
    void DrawCall();
};
```

```cpp
ScreenBuffer& Screen::GetCurrentBuffer()
{
    return screenBuffer[currentIdx];
}

void Screen::DrawCall()
{
    currentIdx++;
    currentIdx %= 2;
}
```

<br/>

