---
title: "컴포넌트"

categories:
  - GPP


tags:
  - gameprogrammingpattern
  - oop
  - component
  - decoupling

toc: true
toc_label: Component
toc_sticky: true

sidebar_main: true
---


한 개체가 여러 분야를 커플링 없이 다룰 수 있게 한다.

<br/>

# Motivation

마리오 같은 게임을 만들되 주인공을 Bjron 이라하고 만들어보자. 주인공은 입력을 위한 컨트롤러, 지형이나 발판 같은 레벨과의 상호작용을 위한 물리 및 충돌, 화면에 나올 애니메이션과 같은 렌더링이나 소리등이 필요하다.

점점 복잡해진다. 소프트웨어 구조 입문 수업에서도 분야가 다른 코드들은 서로 격리해야 한다 배웠다.

AI, 물리, 렌더링, 사운드 처럼 분야가 다른 코드끼리는 최대한 서로 모르는 편이 낫다. 이런 클래스는 기능보다 버그가 빨리 늘어나게 된다.

<br/>

## 고르디우스의 매듭

커플링은 문제가 될 가능성이 크다. 여러 게임 시스템의 주인공 클래스 안에서 실타래 처럼 얽혀있다.

```cpp
if (collidingWithFloor() && (getRenderState() != INVISIBLE))
{
  playSound(HIT_FLOOR);
}
```

위 코드에선 collidingWithFloor(물리), getRenderState(그래픽), playSound(사운드) 를 모두 알아야 고칠 수 있다.

커플링과 코드 길이 문제는 서로 악영향을 미친다. 한 클래스가 너무 많은 분야를 건드리면 모든 프로그래머가 그 클래스를 작업해야 하는데, 클래스가 너무 크니 작업하기 어렵다.

<br/>

## 매듭 끊기

한 덩어리였던 Bjorn 클래스를 분야에 따라 여러 부분으로 나눈다. 예를 들어 사용자 입력과 관련된 코드는 InputComponent 로 옮긴 뒤에 Bjorn 에서는 InputComponent 인스턴스를 갖게 만든다. 여기서 다루는 나머지 코드들도 이런 식으로 반복한다.

이런 이후엔 Bjorn 클래스에는 남는게 거의 없다. 이 상황이 되면 클래스를 여러 작은 클래스로 나누는 이득 이상의 이익이 생긴다.

<br/>

## 열린 구조

컴포넌트 클래스들은 디커플링 되어있다. PhysicsComponent 와 GraphicsComponent 는 Bjorn 클래스 안에 있지만 서로에 대해 알지 못한다. 즉, 물리 프로그래머는 그래픽 처리에 신경쓰지 않고도 자기 코드를 수정할 수 있다.

하지만, 현실적으로는 서로 상호작용이 필요할 수 있다. 예를 들어 AI는 Bjron가 가려는 곳을 물리 컴포넌트를 통해 알아내야 할 수도 있다. 다만, 모든 코드를 한 곳에 섞어놓지 않았기에 통신이 필요한 컴포넌트 들 끼리만 결합을 제한할 수 있다.

<br/>

## 다시 합치기

컴포넌트 패턴의 다른 특징은 이렇게 만든 컴포넌트를 재사용할 수 있다. 다른 객체들을 떠올려보자.

Decoration 은 덤불이나 먼지처럼 볼 수는 있지만 상호작용은 할 수 없다. Prop은 상자, 바위, 나무 같이 볼 수 있으면서 상호작용도 할 수 있다. Zone 은 보이지는 않지만 상호작용은 가능하다.

예를 들어  Bjron가 특정 영역에 들어오면 컷신을 틀고 싶다면 존을 사용할 수 있다.

컴포넌트를 사용하지 않는다면 이 클래스들을 이렇게 상속할 듯 하다.

![dcp_1](/assets/images/gameprogrammingpattern/dcp_1.png)

GameObject 클래스에는 위치나 방향같은 기본 데이터를 둔다. Zone 은 GameObject 를 상속받고 충돌 검사를 추가한다. Decoration 도 Gameobject 를 상속받은 뒤 렌더링 기능을 추가한다. Prop 은 충돌 검사 기능 개선을 위해 Zone 을 상속받는다. 여기서 Prop이 렌더링을 위해 Decoration 을 상속받는 순간 '죽음의 다이아몬드'라고 불리는 다중 상속 문제를 피할 수 없다.

컴포넌트로 만들어보자. 상속이 필요 없이 Gameobject 하나와 PhysicsComponent, GraphicsComponent 클래스 두 개만 있으면 된다. 데커레이션은 GraphicsComponent 만 있게 하면 된다. Zone 에는 PhysicsComponent 만 있다. 프롭에는 둘 다 있다.

컴포넌트는 기본적으로 객체의 위한 플러그 앤 플레이라 볼 수 있다. 객체 소켓에 재사용 가능한 여러 컴포넌트 객체를 꽂아 넣음으로 복잡하면서 기능이 풍부한 개체를 만들 수 있다.

<br/>

# 패턴

여러 분야를 다루는 하나의 개체가 있다. 분야별로 격리하기 위해 각각의 코드를 별도의 컴포넌트 클래스에 둔다. 이제 개체 클래스는 단순히 이들의 컴포넌트 컨테이너 역할만 한다.

<br/>

# 언제 쓸 것인가?

컴포넌트는 게임 개체를 정의하는 핵심 클래스에서 가장 많이 사용되지만, 다음 조건 중 하나라도 만족하면 유용하게 사용 가능하다.

* 한 클래스에서 여러 분야를 건드리고 있어, 이들을 서로 디커플링 하고싶다.
* 클래스가 거대해져서 작업하기가 어렵다.
* 여러 다른 기능을 공유하는 다양한 객체를 정의하고 싶다.

<br/>

# 주의사항

컴포넌트 패턴을 적용하면 한 곳에 코드를 모아놨을 때 보다 더 복잡해질 가능성이 높다. 한 무리의 객체를 생성하고 초기화하고 알맞게 묶어줘야 하나의 개념적인 '객체'를 만들 수 있다. 컴포넌트 끼리의 통신도 더 어렵고, 컴포넌트를 메모리 어디에 배치할 지도 복잡해진다.

코드베이스 규모가 크면 이런 복잡성에서 오는 손해보다 컴포넌트를 통한 코드 재사용에서 얻는 이득이 더 많을 수 있다. 하지만 컴포넌트 패턴을 적용하기 전에 있지도 않은 문제에 대한 해결책을 오버엔지니어링 하는 것은 아닌지 주의해야 한다.

컴포넌트 패턴의 또 다른 문제는 무엇이든지 해결하기 위해선 한 단계를 거쳐야 할 때가 많아진다. 무슨 일이든 컨테이너 객체에서 원하는 컴포넌트부터 얻어야 할 수 있다. 성능이 민감한 내부 루프 코드에서 이런 식으로 포인터를 따라가면 성능이 떨어 질 수 있다.

<br/>

# 예제 코드

## 한곳에 몰아넣은 클래스

우선 컴포넌트 패턴을 적용하지 않아 모든 기능이 때려박힌 클래스부터 만들어보자.

```cpp
class Bjorn
{
public:
  Bjorn()
  : velocity_(0),
    x_(0), y_(0)
  {}

  void update(World& world, Graphics& graphics);

private:
  static const int WALK_ACCELERATION = 1;

  int velocity_;
  int x_, y_;

  Volume volume_;

  Sprite spriteStand_;
  Sprite spriteWalkLeft_;
  Sprite spriteWalkRight_;
};

void Bjorn::update(World& world, Graphics& graphics)
{
  // Apply user input to hero's velocity.
  switch (Controller::getJoystickDirection())
  {
    case DIR_LEFT:
      velocity_ -= WALK_ACCELERATION;
      break;

    case DIR_RIGHT:
      velocity_ += WALK_ACCELERATION;
      break;
  }

  // Modify position by velocity.
  x_ += velocity_;
  world.resolveCollision(volume_, x_, y_, velocity_);

  // Draw the appropriate sprite.
  Sprite* sprite = &spriteStand_;
  if (velocity_ < 0)
  {
    sprite = &spriteWalkLeft_;
  }
  else if (velocity_ > 0)
  {
    sprite = &spriteWalkRight_;
  }

  graphics.draw(*sprite, x_, y_);
}
```

이 코드는 입력에 따라 주인공을 이동하고, 물리 엔진을 통해 다음 위치를 구하고, 화면에 주인공을 그린다.

구현은 간단하다. 별다른게 없기도 하고 세세한 구현도 없다. 하지만, update 한 곳에서 여러 분야의 프로그래머가 작업해야 하고 코드가 더러워지기 시작했다. 이런 코드가 더 복잡해지면 괴로워진다.

<br/>

## 분야별로 나누기

먼저 분야를 하나 정해 Bjorn 에서 별도의 컴포넌트 클래스로 옮긴다. 가장 먼저 처리되는 입력 분야부터 시작하자.

```cpp
class InputComponent
{
public:
  void update(Bjorn& bjorn)
  {
    switch (Controller::getJoystickDirection())
    {
      case DIR_LEFT:
        bjorn.velocity -= WALK_ACCELERATION;
        break;

      case DIR_RIGHT:
        bjorn.velocity += WALK_ACCELERATION;
        break;
    }
  }

private:
  static const int WALK_ACCELERATION = 1;
};

class Bjorn
{
public:
  int velocity;
  int x, y;

  void update(World& world, Graphics& graphics)
  {
    input_.update(*this);

    // Modify position by velocity.
    x += velocity;
    world.resolveCollision(volume_, x, y, velocity);

    // Draw the appropriate sprite.
    Sprite* sprite = &spriteStand_;
    if (velocity < 0)
    {
      sprite = &spriteWalkLeft_;
    }
    else if (velocity > 0)
    {
      sprite = &spriteWalkRight_;
    }

    graphics.draw(*sprite, x, y);
  }

private:
  InputComponent input_;

  Volume volume_;

  Sprite spriteStand_;
  Sprite spriteWalkLeft_;
  Sprite spriteWalkRight_;
};
```

Bjorn 클래스가 이제 Controller 참조하지 않도록 커플링이 일부 제거되었다.

<br/>

## 나머지도 나누기

```cpp
class PhysicsComponent
{
public:
  void update(Bjorn& bjorn, World& world)
  {
    bjorn.x += bjorn.velocity;
    world.resolveCollision(volume_,
        bjorn.x, bjorn.y, bjorn.velocity);
  }

private:
  Volume volume_;
};

class GraphicsComponent
{
public:
  void update(Bjorn& bjorn, Graphics& graphics)
  {
    Sprite* sprite = &spriteStand_;
    if (bjorn.velocity < 0)
    {
      sprite = &spriteWalkLeft_;
    }
    else if (bjorn.velocity > 0)
    {
      sprite = &spriteWalkRight_;
    }

    graphics.draw(*sprite, bjorn.x, bjorn.y);
  }

private:
  Sprite spriteStand_;
  Sprite spriteWalkLeft_;
  Sprite spriteWalkRight_;
};

class Bjorn
{
public:
  int velocity;
  int x, y;

  void update(World& world, Graphics& graphics)
  {
    input_.update(*this);
    physics_.update(*this, world);
    graphics_.update(*this, graphics);
  }

private:
  InputComponent input_;
  PhysicsComponent physics_;
  GraphicsComponent graphics_;
};
```

이제 Bjorn 클래스는 코드가 거의 없다. Bjorn 클래스는 두 가지 역할을 담당한다. 컴포넌트 집합을 관리하고 컴포넌트들이 공유하는 상태를 들고 있는 역할이다. 위치와 속도 값을 Bjorn 클래스에 남겨놓은 이유는 두 가지이다. 이들 상태는 전 분야에서 사용된다. 컴포넌트로 옮기고 싶어도 거의 모든 컴포넌트에서 사용되다 보니 어느 컴포넌트에 넣을 지가 애매하다.

그보다 더 중요한 이유는 이렇게 하면 컴포넌트들이 서로 커플링되지 않고도 쉽게 통신할 수 있기 때문이다.

<br/>

## AI-Bjorn

동작 코드를 별개의 컴포넌트 클래스로 분리했지만 추상화 하진 않았다. Bjorn 클래스는 자신의 동작을 어떤 구체 클래스에서 정의하는지 명확히 알고 싶다. 

사용자 입력 처리 컴포넌트를 인터페이스 뒤로 숨기려고 한다. InputComponent 를 다음처럼 추상 상위 클래스로 바꾼다.

```cpp
class InputComponent
{
public:
  virtual ~InputComponent() {}
  virtual void update(Bjorn& bjorn) = 0;
};

class PlayerInputComponent : public InputComponent
{
public:
  virtual void update(Bjorn& bjorn)
  {
    switch (Controller::getJoystickDirection())
    {
      case DIR_LEFT:
        bjorn.velocity -= WALK_ACCELERATION;
        break;

      case DIR_RIGHT:
        bjorn.velocity += WALK_ACCELERATION;
        break;
    }
  }

private:
  static const int WALK_ACCELERATION = 1;
};

class Bjorn
{
public:
  int velocity;
  int x, y;

  Bjorn(InputComponent* input)
  : input_(input)
  {}

  void update(World& world, Graphics& graphics)
  {
    input_->update(*this);
    physics_.update(*this, world);
    graphics_.update(*this, graphics);
  }

private:
  InputComponent* input_;
  PhysicsComponent physics_;
  GraphicsComponent graphics_;
};
```

이제 Bjorn 객체를 생성할 때 입력 컴포넌트를 아래 처럼 전달할 수 있다.

```cpp
Bjorn* bjorn = new Bjorn(new PlayerInputComponent());
```

어떤 클래스라도 InputComponent 추상 인터페이스만 구현하면 입력 컴포넌트가 될 수 있다. update 가 가상 메소드로 바뀌면서 속도가 조금 느려졌다. 대신 다른 장점도 있다.

대부분의 콘솔 모드는 '데모 모드' 를 지원해야 한다. 플레이어가 주 메뉴에서 아무것도 안 하면, 컴포터가 자동으로 게임을 플레이 하는 모드이다. 이러면 화면에 주 메뉴만 나오는 것을 막을 수 있고, 매장에서도 게임을 근사하게 보여줄 수 있다.

입력 컴포넌트 클래스를 인터페이스 밑에 숨겼기 때문에 이런 걸 만들 수 있게 됬다.

```cpp
class DemoInputComponent : public InputComponent
{
public:
  virtual void update(Bjorn& bjorn)
  {
    // AI to automatically control Bjorn...
  }
};
```

데모 모드 용으로 Bjorn 객체를 생성할 때에는 새로 만든 컴포넌트를 연결하면 된다.

```cpp
Bjorn* bjorn = new Bjorn(new DemoInputComponent());
```

<br/>

## 꼭 Bjorn 일 필요는 없잖아?

이제 Bjorn 은 컴포넌트 묶음일 뿐 딱히 Bjorn 에 관련된 코드가 남아 있지 않다. 그보다는 게임에서 모든 객체가 기본으로 사용하는 게임 객체 클래스로 바꾸는 편이 더 좋을 것 같다.

나머지 물리와 그래픽스 컴포넌트도 입력에서 그랬던 것 처럼 인터페이스와 구현부를 나눈다.

```cpp
class PhysicsComponent
{
public:
  virtual ~PhysicsComponent() {}
  virtual void update(GameObject& obj, World& world) = 0;
};

class GraphicsComponent
{
public:
  virtual ~GraphicsComponent() {}
  virtual void update(GameObject& obj, Graphics& graphics) = 0;
};

class GameObject
{
public:
  int velocity;
  int x, y;

  GameObject(InputComponent* input,
             PhysicsComponent* physics,
             GraphicsComponent* graphics)
  : input_(input),
    physics_(physics),
    graphics_(graphics)
  {}

  void update(World& world, Graphics& graphics)
  {
    input_->update(*this);
    physics_->update(*this, world);
    graphics_->update(*this, graphics);
  }

private:
  InputComponent* input_;
  PhysicsComponent* physics_;
  GraphicsComponent* graphics_;
};

class BjornPhysicsComponent : public PhysicsComponent
{
public:
  virtual void update(GameObject& obj, World& world)
  {
    // Physics code...
  }
};

class BjornGraphicsComponent : public GraphicsComponent
{
public:
  virtual void update(GameObject& obj, Graphics& graphics)
  {
    // Graphics code...
  }
};
```

Bjorn 의 원래 기능을 유지하는 객체를 다음처럼 만들 수 있다.

```cpp
GameObject* createBjorn()
{
  return new GameObject(new PlayerInputComponent(),
                        new BjornPhysicsComponent(),
                        new BjornGraphicsComponent());
}
```

<br/>

# 디자인 결정

컴포넌트 패턴에서 가장 중요한 질문은 '어떤 컴포넌트 집합이 필요한가?' 이다. 대답은 만들고 있는 게임의 장르마다 다르다. 게임의 코드가 크고 복잡할수록 컴포넌트를 더 세분화 해야 할 것이다.

그 밖에도 고려해야 할 것들이 있다.

<br/>

## 객체는 컴포넌트를 어떻게 얻는가/

### 객체가 필요한 컴포넌트를 알아서 생성할 때

* 객체는 항상 필요한 컴포넌트를 가지게 된다
* 객체를 변경하기가 어렵다

<br/>

### 위부 코드에서 컴포넌트를 제공할 때

* 객체가 훨씬 유연해진다.
* 객체를 구체 컴포넌트 자료형으로 부터 디커플링 할 수 있다.

<br/>

## 컴포넌트끼리는 어떻게 통신할 것인가?

컴포넌트들이 완벽하게 격리된 채로 동작하는 게 이상적이지만 실제론 어렵다. 이들 컴포넌트는 한 객체를 이루는 부분이기 때문에 서로 간의 통신이 필요하다.

<br/>

### 컨테이너 객체의 상태를 변경하는 방식

* 컴포넌트들은 서로 디커플링 상태를 유지한다
* 컴포넌트들이 공유하는 정보를 컨테이너 객체에 모두 넣어야 한다

<br/>

### 컴포넌트가 서로 참조하는 방식

서로 통신해야 하는 컴포넌트들이 컨테이너 객체를 통하지 직접 참조하게 만드는 방법이다. Bjorn 이 점프할 수 있게 만들어보자. 그래픽스는 스프라이트를 그려야 하고, 물리엔진은 땅에 있는지를 봐야 한다. 그래픽스와 물리 컴포넌트들이 서로 안다면 쉽게 해결 가능하다.

```cpp
class BjornGraphicsComponent
{
public:
  BjornGraphicsComponent(BjornPhysicsComponent* physics)
  : physics_(physics)
  {}

  void Update(GameObject& obj, Graphics& graphics)
  {
    Sprite* sprite;
    if (!physics_->isOnGround())
    {
      sprite = &spriteJump_;
    }
    else
    {
      // Existing graphics code...
    }

    graphics.draw(*sprite, obj.x, obj.y);
  }

private:
  BjornPhysicsComponent* physics_;

  Sprite spriteStand_;
  Sprite spriteWalkLeft_;
  Sprite spriteWalkRight_;
  Sprite spriteJump_;
};
```

* 간단하고 빠르다
* 두 컴포넌트가 강하게 결합된다

<br/>

### 메세지를 전달하는 방식

가장 복잡한 대안이다. 컨테이너 객체에 간단한 메시징 시스템을 만든 이후에 각 컴포넌트들이 서로에게 정보를 뿌리게 할 수 있다.

```cpp
class Component
{
public:
  virtual ~Component() {}
  virtual void receive(int message) = 0;
};

class ContainerObject
{
public:
  void send(int message)
  {
    for (int i = 0; i < MAX_COMPONENTS; i++)
    {
      if (components_[i] != NULL)
      {
        components_[i]->receive(message);
      }
    }
  }

private:
  static const int MAX_COMPONENTS = 10;
  Component* components_[MAX_COMPONENTS];
};
```

컴포넌트가 컨테이너에 메시지를 보내면 컨테이너는 모든 컴포넌트에 전달한다.

* 하위 컴포넌트들은 디커플링 된다
* 컨테이너 객체는 단ㄷ순하다