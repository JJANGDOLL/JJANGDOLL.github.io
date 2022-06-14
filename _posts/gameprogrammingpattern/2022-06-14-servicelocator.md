---
title: "서비스 중개자"

categories:
  - GPP


tags:
  - gameprogrammingpattern
  - oop
  - service locator

toc: true
toc_label: Service Locator
toc_sticky: true

sidebar_main: true
---


서비스를 구현한 구체 클래스를 숨긴 채로 어디에서나 서비스에 접근할 수 있게 만든다.

<br/>

# Motivation

거의 모든 코드에서 사용되는 것 들이 있다. 메모리 할당, 로그, 난수 생성들은 많은 위치에서 사용된다. 이런 시스템은 게임 전체에서 사용 가능해야 하는 일종의 서비스라고 볼 수 있다. 예를 들어 오디오 시스템은 게임 여러 시스템들과 연결되어 있다.

```cpp
// Use a static class?
AudioSystem::playSound(VERY_LOUD_BANG);

// Or maybe a singleton?
AudioSystem::instance()->playSound(VERY_LOUD_BANG);
```

둘 다 원하는 결과를 얻을 수 있지만 강한 커플링이 생긴다. 오디오 시스템에 접근하는 모든 곳에서 Audiosystem 이라는 클래스 뿐 아니라 정적 클래스나 싱글턴으로 만든 접근 매커니즘 까지 참조하게 된다.

물론 사운드를 출력하기 위한 어느정도의 커플링은 피할 수 없다. 하지만 오디오를 구현한 구체 클래스를 바로 접근할 수 있게 하는 건 좋은 방법은 아니다

서비스 중개자 패턴은 서비스를 사용하는 코드로부터 서비스가 누구인지, 어디에 있는지를 몰라도 되게 해준다.

<br/>

# 패턴

서비스는 여러 기능을 추상 인터페이스로 정의한다. 구체 서비스 제공자는 이런 서비스 인터페이스를 제공받아 구현한다. 이외 별도인 서비스 중개자는 서비스 제공자의 자료형과 이를 등록하는 과정은 숨긴 채 적절한 서비스 제공자를 찾아 서비스에 대한 접근을 제공한다.

<br/>

# 언제 쓸 것인가?

무엇이든 프로그램 어디에서나 접근하게 하면 문제가 생기기 쉽다. 적절하게 사용해야 한다.

접근해야 할 객체가 있다면 전역 매커니즘 대신 객체를 인자로 넘겨주는 것 부터 고려하자. 이렇게 하면 커플링을 명확히 보여줄 수 있다.

하지만, 인자로 넘겨주는 것이 불필요하거나 코드를 읽는 데 방해가 되기도 한다. 렌더링 함수에는 렌더링에 관련된 것만 있어야지 로그 같은게 섞이면 곤란하다.

또한, 어떤 시스템은 본질적으로 하나뿐이다. 

이럴 때 서비스 중개자 패턴으로부터 도움을 받을 수 있다. 이 패턴은 더 유연하고 더 설정하기 좋은 싱글턴 패턴이다. 잘만 사용하면 런타임 비용은 거의 들이지 않고 유연한 코드를 만들 수 있다.

<br/>

# 주의사항

두 코드가 커플링되는 의존성을 런타임 시점까지 미루는 부분이 가장 어렵다. 코드만 봐서는 어떤 의존성을 사용하는 지를 알기 어렵다.

<br/>

## 서비스가 실제로 등록되어 있어야 한다

싱글턴이나 정적 클래스는 인스턴스가 항상 준비되어 있다. 하지만 여기선 서비스 객체를 등록해주어야 한다.

<br/>

## 서비스는 누가 자기를 가져다 놓는지 모른다

서비스 중개자는 전역에서 접근 가능하기 때문에 모든 코드에서 서비스를 요청하고 접근할 수 있다. 즉, 어디서나 문제없이 돌아가야 한다. 특정 상황에서만 실행되어야 한다면 적용하지 않는 편이 안전하다.

<br/>

# 예제 코드

## 서비스

```cpp
class Audio
{
public:
  virtual ~Audio() {}
  virtual void playSound(int soundID) = 0;
  virtual void stopSound(int soundID) = 0;
  virtual void stopAllSounds() = 0;
};
```

<br/>

## 서비스 중개자

```cpp
class ConsoleAudio : public Audio
{
public:
  virtual void playSound(int soundID)
  {
    // Play sound using console audio api...
  }

  virtual void stopSound(int soundID)
  {
    // Stop sound using console audio api...
  }

  virtual void stopAllSounds()
  {
    // Stop all sounds using console audio api...
  }
};
```

## 단순한 중개자

```cpp
class Locator
{
public:
  static Audio* getAudio() { return service_; }

  static void provide(Audio* service)
  {
    service_ = service;
  }

private:
  static Audio* service_;
};
```

getAudio 가 중개 역할을 하고있다. 어디에서나 부를 수 있는 getAudio 함수는 Audio 인스턴스를 반환한다.

```cpp
Audio *audio = Locator::getAudio();
audio->playSound(VERY_LOUD_BANG);
```

Locator 에 등록하는 방법도 간단하다.

```cpp
ConsoleAudio *audio = new ConsoleAudio();
Locator::provide(audio);
```

playSound 를 실행하는 쪽에선 추상 인터페이스만 알 뿐 ConsoleAudio 라는 구체 클래스에 대해선 전혀 모른다는 것이 핵심이다. Locator 역시 서비스 제공자의 구체 클래스와는 커플링 되지 않는다. 어떤 구체 클래스가 실제로 사용되는지는 서비스를 제공하는 초기화 코드에서만 알 수 있다. Audio 인터페이스도 자기가 중개자를 통해서 여기저기서 접근된다는 사실을 모른다.

<br/>

## 널서비스

만약 서비스 제공자가 서비스를 등록하기 전에 서비스를 사용하려 한다면 NULL을 반환한다. 이때 호출하는 쪽에서 널체크를 하지 않으면 크래시가 발생한다.

이럴 때 써먹을 수 있는 널 객체 디자인 패턴이 있다.

```cpp
class NullAudio: public Audio
{
public:
  virtual void playSound(int soundID) { /* Do nothing. */ }
  virtual void stopSound(int soundID) { /* Do nothing. */ }
  virtual void stopAllSounds()        { /* Do nothing. */ }
};

class Locator
{
public:
  static void initialize() { service_ = &nullService_; }

  static Audio& getAudio() { return *service_; }

  static void provide(Audio* service)
  {
    if (service == NULL)
    {
      // Revert to null service.
      service_ = &nullService_;
    }
    else
    {
      service_ = service;
    }
  }

private:
  static Audio* service_;
  static NullAudio nullService_;
};
```

이러면 호출하는 쪽에서는 서비스가 준비되어 있는지를 신경쓰지 않고 널체크를 할 필요도 없다. 다만, 의도한대로 동작하지 않을 수도 있다.

널 서비스는 의도적으로 특정 서비스를 못 찾게 하고 싶을 때에도 유용하다. 어떤 시슽메을 잠시 못쓰게 하고 싶다면 서비스를 그냥 등록하지 않으면 된다.

<br/>

## 로그 데커레이터

개발하다 보면 여기저기서 로그를 설치해야 할 때가 있다. 보통 log 함수를 여기저기에 집어넣는데 이러다 보면 로그가 너무 많아진다. 원하는 로그만 켰다 껏다 할 수 있고 조건적으로 로그를 남기고 싶은 시스템이 서비스로 노출되어 있다면, GOF의 데커레이터 패턴으로 해결할 수 있다.

```cpp
class LoggedAudio : public Audio
{
public:
  LoggedAudio(Audio &wrapped)
  : wrapped_(wrapped)
  {}

  virtual void playSound(int soundID)
  {
    log("play sound");
    wrapped_.playSound(soundID);
  }

  virtual void stopSound(int soundID)
  {
    log("stop sound");
    wrapped_.stopSound(soundID);
  }

  virtual void stopAllSounds()
  {
    log("stop all sounds");
    wrapped_.stopAllSounds();
  }

private:
  void log(const char* message)
  {
    // Code to log message...
  }

  Audio &wrapped_;
};

void enableAudioLogging()
{
  // Decorate the existing service.
  Audio *service = new LoggedAudio(Locator::getAudio());

  // Swap it in.
  Locator::provide(service);
}
```

<br/>

# 디자인 결정

## 서비스는 어떻게 등록되는가?

### 외부 코드에서 등록

* 빠르고 간단하다
* 서비스 제공자를 어떻게 만들지 제어할 수 있다
* 게임 실행 도중 서비스를 교체할 수 있다
* 서비스 중개자가 외부 코드와 의존한다

<br/>

### 컴파일 할 때 바인딩

```cpp
class Locator
{
public:
  static Audio& getAudio() { return service_; }

private:
  #if DEBUG
    static DebugAudio service_;
  #else
    static ReleaseAudio service_;
  #endif
};
```

* 빠르다
* 서비스는 항상 사용 가능하다
* 서비스를 쉽게 변경할 수 없다

<br/>

### 런타임 설정에 값 읽기

* 다시 컴파일 하지 않고도 서비스를 교체할 수 있다
* 프로그래머가 아니어도 서비스를 바꿀 수 있다
* 등록 과정을 코드에서 완전히 뺴냈기에 하나의 코드로 여러 설정을 동시에 지원할 수 있다
* 복잡하다
* 서비스 등록에 시간이 걸린다

<br/>

## 서비스를 못 찾으면 어떻게 할 것인가?

### 사용자가 알아서 처리하게 한다

* 실패시 처리를 사용자가 정할 수 있다
* 실패시 처리를 사용자가 정해야 한다

<br/>

### 게임을 멈춘다

```cpp
class Locator
{
public:
  static Audio& getAudio()
  {
    Audio* service = NULL;

    // Code here to locate service...

    assert(service != NULL);
    return *service;
  }
};
```

* 실패시 처리를 사용자가 정하지 않아도 된다
* 게임이 멈춘다

<br/>

### 널 서비스를 반환한다

* 외부 코드에서 서비스가 없는 경우를 처리하지 않아도 된다
* 서비스가 사용할 수 없는데도 게임을 진행할 수 있다

<br/>

## 서비스의 범위는 어떻게 잡을 것인가?

특정 클래스 및 하위 클래스에서만 접근을 제한할 수 있다.

```cpp
class Base
{
  // Code to locate service and set service_...

protected:
  // Derived classes can use service
  static Audio& getAudio() { return *service_; }

private:
  static Audio* service_;
};
```

물론 장단점이 있다

<br/>

### 전역에서 접근 가능한 경우

* 전체 코드에서 같은 서비스를 쓰도록 한다
* 언제 어디에서 서비스가 사용되는 지를 제어할 수 없다

<br/>

### 접근이 특정 클래스에 제한되면

* 커플링을 제어할 수 있다
* 중복 작업을 해야 할 수 있다는 단점이 있다

