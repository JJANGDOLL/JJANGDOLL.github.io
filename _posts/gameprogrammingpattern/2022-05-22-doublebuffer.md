---
title: "이중 버퍼"

categories:
  - GPP


tags:
  - gameprogrammingpattern
  - oop
  - state

toc: true
toc_label: State
toc_sticky: true

sidebar_main: true
---

일련의 순차적 작업이 동시에 나타나도록 합니다.

<br/>

# Motivation

본질적으로 컴퓨터는 순차적으로 동작한다. 하지만 사용자 입장에서는 모든 작업을 한번에 모아 봐야 할 경우가 생긴다.

대표적으로 게임의 렌더링은 꼭 필요하다. 유저에게 보여줄 화면을 그리는 작업은 빠르고 프레임이 완성되면 한 번에 보여줘야 한다.

이중 버퍼는 이런 문제를 해결한다.

<br/>

## 그래픽스

거의 모든 게임의 렌더링 시스템이 내부에서 이렇게 동작한다. 프레임 버퍼를 두 개 준비해, 하나는 지금 프레임에 보일 값을 저장해 GPU가 원할 때 언제든 읽게 한다.

그 동안 다른 버퍼에는 렌더링 코드가 채운다. 렌더링 코드가 장면을 다 그린 후 버퍼를 교체한 뒤 하드웨어이 두 번째 버퍼를 읽으라고 알려준다.

그 사이에 교체된 이전 프레임버퍼에 다음 프레임에 그릴 화면을 저장하면 된다!

<br/>

# 패턴

버퍼 클래스는 변경이 가능한 버퍼를 캡슐화 한다. 버퍼는 수정되지만 밖에서는 한 번에 바뀌는 것 처럼 보이고 싶다. 따라서, 버퍼 클래스는 현재 버퍼와 다음 버퍼 총 두 개의 버퍼를 가진다.

정보를 읽을 때는 항상 현재 버퍼에 접근한다. 정보를 쓸 때는 항상 다음 버퍼에 접근한다. 변경이 끝나면 다음 버퍼와 현재 버퍼를 교체해 다음 버퍼가 보여지게 한다.

<br/>

# 사용처

* 순차적으로 변경해야 하는 상태가 있다
* 이 상태는 변경 도중에도 접근 가능해야 한다
* 밖의 코드에서 작업중인 상태에 접근 할 순 없어야 한다.
* 상태에 값을 쓰는 도중에도 기다리지 않고 바로 접근할 수 있어야 한다

<br/>

# 주의사항

## 교체 연산 자체에 시간이 걸린다

이중 버퍼 패턴에서 버퍼에 값 입력이 끝나면 버퍼를 교체해야 한다. 교체 연산은 원자적(atomic) 이여야 한다. 즉, 교체 중에는 두 버퍼 모두에 접근할 수 없어야 한다. 대부분 포인터만 변경해서 빠르지만, 혹시라도 버퍼에 값을 쓰는 것 보다 교체가 더 오래 걸린다면 이 패턴이 아무런 도움이 안된다.

<br/>

## 버퍼가 두 개 필요하다

이중 버퍼 패턴은 메모리가 더 필요하다. 따라서, 메모리가 부족한 기기에서는 부담이 될 수 있다.

<br/>

# 그래픽스 예제

프레임 버퍼에 픽셀을 그릴 수 있는 아주 단순한 그래픽 시스템을 만든다.

```cpp
class Framebuffer
{
public:
  Framebuffer() { clear(); }

  void clear()
  {
    for (int i = 0; i < WIDTH * HEIGHT; i++)
    {
      pixels_[i] = WHITE;
    }
  }

  void draw(int x, int y)
  {
    pixels_[(WIDTH * y) + x] = BLACK;
  }

  const char* getPixels()
  {
    return pixels_;
  }

private:
  static const int WIDTH = 160;
  static const int HEIGHT = 120;

  char pixels_[WIDTH * HEIGHT];
};
```

여기선 clear 메소드로 전체 버퍼를 흰색으로 지우거나, draw 메소드로 특정픽셀에 검정색을 입힐 수 있다. getPixels 을 통해 메모리 배열에 접근할 수도 있다.

이걸 Scene 클래스에 넣는다.

```cpp
class Scene
{
public:
  void draw()
  {
    buffer_.clear();

    buffer_.draw(1, 1);
    buffer_.draw(4, 1);
    buffer_.draw(1, 3);
    buffer_.draw(2, 4);
    buffer_.draw(3, 4);
    buffer_.draw(4, 3);
  }

  Framebuffer& getBuffer() { return buffer_; }

private:
  Framebuffer buffer_;
};
```

게임 코드에선 매 프레임마다 어떤 장면을 그려야 할 지 알려준다. 우선 버퍼를 지운뒤 그리고자 하는 위치에 검은색을 찍는다. 내부 버퍼에 접근 가능한 getBuffer 메소드를 제공한다.

복잡하지 않지만 문제가 생길 수 있다. 비디오 드라이버가 아무때나 getPixel 을 호출해 버퍼에 접근할 가능성이 있다.

```cpp
buffer_.draw(1, 1);
buffer_.draw(4, 1);
buffer_.draw(1, 3);
// <== 여기서 접근해서 읽을 수도 있다!

buffer_.draw(2, 4);
buffer_.draw(3, 4);
buffer_.draw(4, 3);
```

이렇게 되면 그림이 짤리게 된다. 이렇게 되면 플리커링 현상을 볼 수 있다. 이 문제를 이중 버퍼로 해결할 수 있다.

```cpp
class Scene
{
public:
  Scene()
  : current_(&buffers_[0]),
    next_(&buffers_[1])
  {}

  void draw()
  {
    next_->clear();

    next_->draw(1, 1);
    // ...
    next_->draw(4, 3);

    swap();
  }

  Framebuffer& getBuffer() { return *current_; }

private:
  void swap()
  {
    // Just switch the pointers.
    Framebuffer* temp = current_;
    current_ = next_;
    next_ = temp;
  }

  Framebuffer  buffers_[2];
  Framebuffer* current_;
  Framebuffer* next_;
};
```

이제 Scene 에는 두개의 버퍼가 있고 외부에서의 접근은 완성된 버퍼에먼 접근을 한다. 

<br/>

## 그래픽스 외에

변경 중인 상태에 접근할 수 있다는 것이 이중 버퍼가 해결하려는 문제다. 원인은 보통 두가지인데, 다른 스레드나 인터럽트에서 상태에 접근하려는 경우이다. 그 다음이 어떤 상태를 변경하려는 코드가, 동시에 지금 변경하려는 상태를 읽는 경우다. 특히 물리나 인공지능같이 객체가 상호작용 하는 경우 쉽게 볼 수 있다.

<br/>

# 인공지능 예제

이 책의 예제인데, 슬랩스틱 기반의 코미디 기반 게임을 예시로 든다.

무대 위에 배우들이 몸개그를 하고있다.

```cpp
class Actor
{
public:
  Actor() : slapped_(false) {}

  virtual ~Actor() {}
  virtual void update() = 0;

  void reset()      { slapped_ = false; }
  void slap()       { slapped_ = true; }
  bool wasSlapped() { return slapped_; }

private:
  bool slapped_;
};
```

매 프레임마다 actor 의 update 를 호출해 배우가 뭔가를 진행할 수 있는 무대가 필요하고, 모든 배우가 한 번에 업데이트 되는 것처럼 보여야 한다.

배우는 서로 상호작용 할 수 있다. 돌아가면서 때리는 것을 상호작용이라고 부를 수 있다. 이제 배우들이 상호작용 할 수 있는 무대를 만들자.

```cpp
class Stage
{
public:
  void add(Actor* actor, int index)
  {
    actors_[index] = actor;
  }

  void update()
  {
    for (int i = 0; i < NUM_ACTORS; i++)
    {
      actors_[i]->update();
      actors_[i]->reset();
    }
  }

private:
  static const int NUM_ACTORS = 3;

  Actor* actors_[NUM_ACTORS];
};
```

배우들이 딱 한번만 반응하도록 update 이후 reset 한다는 부분도 살펴보자

이후 Actor 를 상속받는 Comedian 클래스도 생성하자.

```cpp
class Comedian : public Actor
{
public:
  void face(Actor* actor) { facing_ = actor; }

  virtual void update()
  {
    if (wasSlapped()) facing_->slap();
  }

private:
  Actor* facing_;
};
```

이후 코미디언을 무대에 세우고 어떤 일이 벌어지는 지 보자.

```cpp
Stage stage;

Comedian* harry = new Comedian();
Comedian* baldy = new Comedian();
Comedian* chump = new Comedian();

harry->face(baldy);
baldy->face(chump);
chump->face(harry);

stage.add(harry, 0);
stage.add(baldy, 1);
stage.add(chump, 2);
```

그림으로 도식화 해서 보면 아래 처럼 배열이 되어있다.


![db_ai_1](/assets/images/gameprogrammingpattern/db_ai_1.png)

이제 Harry 를 때린 후 일어나는 일을 살펴보자.

```cpp
harry->slap();

stage.update();
```

Stege::Update 메소드가 순서대로 돌아가면서 Actor::Update 를 호출하기 시작하고, 플레이어는 이런 결과를 얻게 된다.

1. Harry 가 따귀를 맞았다. Harry 는 Baldy 를 때린다.
2. Baldy 가 따귀를 맞았다. Baldy 는 Chump 를 때린다.
3. Chump 가 따귀를 맞았다. Chump 는 Harry 를 때린다.

결과적으로 Harry 를 때린 결과가 한 프레임만에 모든 코미디언에게 전파되는 것을 플레이어가 볼 수 있다. Stage 에서 배열의 순서만 바꿔보자.

![db_ai_2](/assets/images/gameprogrammingpattern/db_ai_2.png)

이후 Harry 를 때리면 아래처럼 동작한다.

1. Chump 가 따귀를 안 맞았다. 아무것도 하지 않는다.
2. Baldy 가 따귀를 안 맞았다. 아무것도 하지 않는다.
3. Harry 는 따귀를 맞았다. Harry 는 Baldy 를 때린다.

이런이런. 전혀 다른 결과가 나와버렸다. 배우 전체를 업데이트 할 때 마다 배우의 slapped_ 상태를 바꾸는데 동시에 읽기도 하다보니 초반에 맞은 상태를 바꾼게 나중에 가서 영향을 미친다.

결과적으로 배우가 맞았을 때 배치 순서에 따라 이번 프레임에서 나올 수도 있고 다음 프레임에서 나올 수도 있다. 이런 식으로  업데이트 순서에 따라 결과가 다르면 안된다.

<br/>

## 맞은 상태를 버퍼에 저장하기

여기서도 이중버퍼를 써먹을 수 있다. 여기선 배우의 '맞은' 상태만 저장하는 버퍼를 두개 만든다.

```cpp
class Actor
{
public:
  Actor() : currentSlapped_(false) {}

  virtual ~Actor() {}
  virtual void update() = 0;

  void swap()
  {
    // Swap the buffer.
    currentSlapped_ = nextSlapped_;

    // Clear the new "next" buffer.
    nextSlapped_ = false;
  }

  void slap()       { nextSlapped_ = true; }
  bool wasSlapped() { return currentSlapped_; }

private:
  bool currentSlapped_;
  bool nextSlapped_;
};
```

이러면 Stage 도 약간 바껴야 된다.

```cpp
void Stage::update()
{
  for (int i = 0; i < NUM_ACTORS; i++)
  {
    actors_[i]->update();
  }

  for (int i = 0; i < NUM_ACTORS; i++)
  {
    actors_[i]->swap();
  }
}
```

이러면 update 메소드는 모든 배우를 업데이트 한 다음에 상태를 교체한다. 결과적으로 모든 배우는 자신이맞았다는 걸 다음 프레임에 알 수 있다. 하지만 배열 순서와 상관 없이 같은 프레임에 같은 행동을 한다.

<br/>

# 디자인 결정

이중 버퍼 패턴을 구현할 때 결정해야 할 중요한 점을 살펴보자.

<br/>

## 버퍼를 어떻게 교체할 것인가?

버퍼 교체 연산은 두 버퍼 모두 일긱 쓰기를 사용하지 못하게 한다는 점에서 최대한 빠르게 교체해야 한다.

<br/>

### 버퍼 포인터나 레퍼런스를 교체

이중 버퍼 그래픽스에서 가장 일반적으로 사용된다.

* 빠르다
* 버퍼 코드 밖에서는 버퍼 메모리를 포인터로 저장할 수 없다
* 버퍼에 남아있는 데이터는 바로 이전 프레임이 아닌 2 프레임 전 데이터이다

<br/>

### 버퍼끼리 데이터 복사

* 다음 버퍼에는 딱 한프레임 전 데이터가 들어있다
* 교체 시간이 더 걸린다.

<br/>

## 얼마나 정밀하게 버퍼링 할 것인가?

버퍼가 하나의 큰 데이터 덩어리인가? 객체 컬렉션 안에 분산되어 있는가? 그래픽 예제의 경우 전자에 해당하고, 배우 예제는 후자이다.

<br/>

### 버퍼가 한 덩어리라면

* 교체가 간단하다. 포인터만 교체하면 된다.

<br/>

### 여러 객체가 각종 데이터를 들고 있다면

* 교체가 느리다. 컬렉션 전체를 순회하며 교체하라고 알려줘야 한다.

<br/>

