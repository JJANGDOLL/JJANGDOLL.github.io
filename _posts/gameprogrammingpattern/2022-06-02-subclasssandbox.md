---
title: "하위 클래스 샌드박스"

categories:
  - GPP


tags:
  - gameprogrammingpattern
  - oop
  - subclasssandbox

toc: true
toc_label: Subclass Sandbox
toc_sticky: true

sidebar_main: true
---

상위 클래스가 제공하는 기능들을 통해서 하위 클래스에서 행동을 정의한다.

<br/>

# Motivation

어릴 때 꿈꿔왔던 어떤 초능력이라도 다 쓸 수 있는 풍부한 게임 월드를 제공하고 싶다. Superpower 를 상속받은 초능력 클래스에서 사운드, 시각 이펙트, AI와의 상호작용, 다른 게임 개체의 생성과 파괴, 물리 작용 같은 일을 모두 할 수 있어야 한다. 이 클래스는 온갖 코드를 다 건들게된다.

이렇게 개발하면 무슨 일이 벌어질까?

* 중복 코드가 많아진다
* 거의 모든 게임 코드가 초능력 클래스와 커플링된다
* 외부 시스템이 변경되면 초능력 클래스가 깨질 가능성이 높다
* 모든 초능력 클래스가 지켜야 할 불변식을 정의하기 어렵다

초능력 클래스를 구현하는 게임플레이 프로그래머가 사용할 원시명령 집합을 제공하는 게 좋겠다. 사운드 재생을 위한 playSound, 파티클을 위한 spawnParticles 함수를 호출하면 된다. 초능력을 구현하는 데 필요한 모든 기능을 원시명령이 제공하기 때문에 초능력 클래스가 이런저런 헤더를 include 하거나 다른 코드를 찾아 해매지 않아도 된다.

이를 위해 원시 명령들을 Superpower의 protected 에 메소드로 만들어 모든 하위 초능력 클래스에서 쉽게 접근 가능하도록 한다. 원시명령을 protected와 비가상함수로 만드는 이유는 이들 함수가 하위 클래스 용이라는 것을 알려주기 위해서다.

가지고 놀 수 있는 원시명령을 준비하고 나면, 이를 사용할 공간을 제공해야한다. 이를 위해 하위 클래스가 구현해야 하는 샌드박스 메소드를 순수 가상 함수로 만들어 protected 로 둔다. 이제 초능력클래스 구현을 위해선 아래 작업을 해야한다.

1. Superpower를 상속받는 새로운 클래스를 만든다
2. 샌드박스 메소드인 activate()를 오버라이드 한다
3. Superpower 클래스가 제공하는 protected 메소드를 호출하여 activate 를 구현한다

여러 초능력 클래스에서 중복되는 코드가 있다면, 언제든지 Superpower 클래스로 옮겨서 하위 클래스에서 재사용 할 수 있도록 한다.

커플링 문제를 Superpower 클래스로 한곳으로 몰았다. 다른 여러 게임 시스템들은 Superpower 랑만 커플링 될 뿐 다른 코드와는 커플링 되지 않는다.

하위 클래스 샌드박스 패턴을 쓰면 클래스 상속 구조가 얇아진다. 많은 클래스가 Superpower 를 상위 클래스로 두기에 전략적 요충지를 확보할 수 있다. 즉, Superpower 클래스에 시간과 정성을 쏟으면 하위 클래스가 모두 혜택을 볼 수 있다.

<br/>

# 패턴

상위 클래스는 추상 샌드박스 메소드와 여러 제공 기능(provided operation)를 정의한다. 제공 기능은 protected 로 만들어서 하위 클래스 용이라는 것을 명시한다. 하위 클래스는 제공 기능을 구현한다.

<br/>

# 언제 쓸 것인가?

* 클래스 하나에 하위 클래스가 많다
* 상위 클래스는 하위 클래스가필요로 하는 기능을 전부 제공할 수 있다
* 하위 클래스 행동 중에는 겹치는 게 많아, 이를 하위 클래스끼리 공유하고 싶다
* 하위 클래스들 사이의 커플링 및 하위 클래스와 나머지 코드와의 커플링을 최소화 하고 싶다

<br/>

# 주의사항

하위 클래스는 상위 클래스를 통해서 나머지 게임 코드에 접근하기 때문에 상위 클래스가 하위 클래스에서 접근해야 하는 모든 시스템과 커플링된다. 상위 클래스가 조금만 바껴도 어딘가가 깨지기 쉽다. fragile baser class 문제에 빠지기 쉽다.

좋은 점은 커플링 대부분이 상위 클래스에 몰려 있기 때문에 하위 클래스를 나머지 코드와 깔끔하게 분리할 수 있다.

하지만, 상위 클래스 코드가 거대한 스파게티 덩어리가 되어 간다면 제공 기능 일부를 별도로 뽑아내 책임을 나눠 갖는, 컴포넌트 패턴이 도움이 될 것이다.

<br/>

# 예제 코드

간단한 패턴이기에 예제도 간단하다. 이 패턴이 쓸모없다는 얘기는 아니고 의도가 중요하다.

Superpower 상위 클래스부터 만들자.

```cpp
class Superpower
{
public:
  virtual ~Superpower() {}

protected:
  virtual void activate() = 0;

  void move(double x, double y, double z)
  {
    // Code here...
  }

  void playSound(SoundId sound, double volume)
  {
    // Code here...
  }

  void spawnParticles(ParticleType type, int count)
  {
    // Code here...
  }
};
```

activate 가 샌드박스 메소드이다. 순수 가상함수이기에 반드시 하위 클래스에서 구현해야 한다. 나머지 protected 메소드는 하위 클래스에서 activate 를 구현할 때 사용한다.

Superpower 클래스는 제공 기능을 통해 다른 시스템에 접근한다. playSound 는 오디오 엔진 함수를, move 는 물리 코드를 호출하는 식이다. Superpower 에서만 다른 시스템에 접근하기에 이 안에 모든 커플링을 캡슐화 할 수 있다.

이제 스파이더맨의 방사능 거미를 만들어보자.

```cpp
class SkyLaunch : public Superpower
{
protected:
  virtual void activate()
  {
    // Spring into the air.
    playSound(SOUND_SPROING, 1.0f);
    spawnParticles(PARTICLE_DUST, 10);
    move(0, 0, 20);
  }
};
```

이 activate 는 슈퍼히어로를 높이 뛰게 만든다. 모든 초능력 클래스가 단순히 사운드, 이펙트, 모션 조합만 있다면 이 패턴을 쓸 필요가 없다. 대신, 초능력에서는 정해진 동작만 하도록 activate 를 구현하고, 초능력 별로 다른 사운드, 파티클, 움직임을 사용하게 만들면 된다. 하지만, 이런 건 모든 초능력이 본질적으로 동작은 같으면서 데이터가 다를 때만 가능하다. 코드를 좀 더 정교하게 만든다.

```cpp
class Superpower
{
protected:
  double getHeroX()
  {
    // Code here...
  }

  double getHeroY()
  {
    // Code here...
  }

  double getHeroZ()
  {
    // Code here...
  }

  // Existing stuff...
};
```

히어로 위치를 얻는 몇 가지의 메소드를 추가했다. 이제 하위 클래스에서 이들을 사용할 수 있다.

```cpp
class SkyLaunch : public Superpower
{
protected:
  virtual void activate()
  {
    if (getHeroZ() == 0)
    {
      // On the ground, so spring into the air.
      playSound(SOUND_SPROING, 1.0f);
      spawnParticles(PARTICLE_DUST, 10);
      move(0, 0, 20);
    }
    else if (getHeroZ() < 10.0f)
    {
      // Near the ground, so do a double jump.
      playSound(SOUND_SWOOP, 1.0f);
      move(0, 0, getHeroZ() + 20);
    }
    else
    {
      // Way up in the air, so do a dive attack.
      playSound(SOUND_DIVE, 0.7f);
      spawnParticles(PARTICLE_SPARKLES, 1);
      move(0, 0, -getHeroZ());
    }
  }
};
```

이제 샌더박스 메소드에서 흥미로운 흐름 제어를 만들 수 있게 되었다. 간단한 if 문 몇 개만 사용했지만 아무 코드나 넣을 수 있으니 마음대로 구현할 수 있다.

<br/>

# 디자인 결정

## 어떤 기능을 제공해야 하나?

* 제공 기능을 몇 안 되는 하위클래스에서만 사용한다면 별 이득이 없다. 다른 제공 기능과 일관성을 유지하는 의의는 있지만 특수한 하위 클래스들에서 직접 접근하는 것이 더 간단하고 명료할 수 있다.
* 다른 시스템 함수를 호출할 때에도 그 함수의 상태를 변경하지 않는다면 '안전한'커플링이다. 하지만, 외부 시스템의 상태를 변경한다면 눈에 더 잘 들어오는 상위 클래스의 제공 기능으로 옮기는게 나을 수 있다.
* 제공 기능이 단순한 외부 시스템으로 호출을 넘기는 것이면 있어봐야 좋을 게 없다. 하지만, 단순히 포워딩 하는 메소드도 하위 클래스의 특정 상태를 숨길 수 있는 장점이 있다.

<br/>

## 메소드를 직접 제공할것인가? 이를 담고있는 객체를 통해서 제공할 것인가?

하위 클래스 샌드박스 패턴의 골칫거리는 상위 클래스의 메소드 수가 너무 많아진다. 이들 메소드 일부를 다른 클래스로 옮기면 문제를 완화할 수 있다. 상위 클래스의 제공 기능에서는 이들 객체를 반환하기만 하면 된다.

초능력을 쓸 때 사운드를 내기 위해 Superpower 클래스에 메소드를 직접 추가할 수 있다.

```cpp
class Superpower
{
protected:
  void playSound(SoundId sound, double volume)
  {
    // Code here...
  }

  void stopSound(SoundId sound)
  {
    // Code here...
  }

  void setVolume(SoundId sound)
  {
    // Code here...
  }

  // Sandbox method and other operations...
};
```

하지만 이미 Superpower 가 복잡하다면 이렇게 추가하고 싶진 않을것이다. 이럴 땐 사운드 기능을 담당하는 SoundPlayer 클래스를 새로 만들자.

```cpp
class SoundPlayer
{
  void playSound(SoundId sound, double volume)
  {
    // Code here...
  }

  void stopSound(SoundId sound)
  {
    // Code here...
  }

  void setVolume(SoundId sound)
  {
    // Code here...
  }
};
```

이후 Superpower 에서는 SoundPlayer 객체에 접근할 수 있게한다.

```cpp
class Superpower
{
protected:
  SoundPlayer& getSoundPlayer()
  {
    return soundPlayer_;
  }

  // Sandbox method and other operations...

private:
  SoundPlayer soundPlayer_;
};
```

* 상위 클래스의 메소드 개수를 줄일 수 있다
* 보조 클래스에 있는 코드가 유지보수하기 더 쉬운 편이다
* 상위 클래스와 다른 시스템과의 커플링을 낮출 수 있다

<br/>

## 상위 클래스는 필요한 객체를 어떻게 얻는가?

예제의 Superpower 클래스의 제공 기능 중에 spawnParticles 를 하기 위해 파티클 시스템 객체가 필요하다면 어떻게 얻을 수 있을가?

<br/>

### 상위 클래스의 생성자로 받기

```cpp
class Superpower
{
public:
  Superpower(ParticleSystem* particles)
  : particles_(particles)
  {}

  // Sandbox method and other operations...

private:
  ParticleSystem* particles_;
};
```

이제 모든 초능력 클래스는 생성될 때 파티클 시스템 객체를 참조하도록 강조되었다. 하지만, 하위클래스를 생각해보자.

```cpp
class SkyLaunch : public Superpower
{
public:
  SkyLaunch(ParticleSystem* particles)
  : Superpower(particles)
  {}
};
```

저런, 문제가 있다. 모든 하위 클래스 생성자는 파티클 시스템을 인수로 받아 상위 클래스로 전달해야해서 원치않게 모든 하위 클래스의 상태가 노출됬다.

상위 클래스에 다른 상태를 추가하려면 하위 클래스 생성자도 해당 상태를 전달하도록 전부 바꿔야 해서 유지보수도 까다롭다.

<br/>

### 2단계 초기화

초기화를 2단계로 나누면 생성자로 모든 상태를 전달하는 번거로움을 피할 수 있다. 생성자는 매개변수 없이 그냥 객체를 생성한다. 그 후 실행에 필요한 데이터를 제공한다.

```cpp
Superpower* power = new SkyLaunch();
power->init(particles);
```

SkyLaunch 클래스 생성자에는 인수가 없어서 Superpower 클래스가 private 로 숨겨놓은 멤버 변수와 커플링되지 않는다. 하지만, 까머깆말고 init을 호출하자. 이 문제는  객체 생성 과정 전체를 한 함술로 캡슐화하면 해결이 된다.

```cpp
Superpower* createSkyLaunch(ParticleSystem* particles)
{
  Superpower* power = new SkyLaunch();
  power->init(particles);
  return power;
}
```

<br/>

## 정적 객체로 만들기

앞에는 초능력 인스턴스별로 파티클 시스템을 초기화 했다. 하지만 인스턴스들이 별도의 파티클 객체를 필요하지 않을 것 같다. 파티클 시스템이 싱글턴이라면 어차피 모든 초능력 인스턴스가 같은 상태를 공유할 것이다.

이럴 때는 상태를 상위 클래스의 private 정적 멤버 변수로 만들 수 있디.

```cpp
class Superpower
{
public:
  static void init(ParticleSystem* particles)
  {
    particles_ = particles;
  }

  // Sandbox method and other operations...

private:
  static ParticleSystem* particles_;
};
```

위 함수와 멤버 변수는 모두 정적이라서 Superpower::init() 한번만 호출하면 해 놓으면 모든 초능력 인스턴스에서 같은 파티클 시스템에 접근 가능하다. 하위 클래스 생성자만 호출하면 Superpower 인스턴스를 그냥 만들 수 있다.

particles_ 가 정적 변수이기에 메모리 사용량을 줄일 수 있다는 것도 장점이다.

<br/>

### 서비스 중개자를 이용하기

앞에서는 상위 클래스가 필요하는 객체를 먼저 넣어주는 작업을 밖에서 잊지 말고 해줘야 했다. 즉, 초기화 부담을 외부 코드에 전가하고 있다. 만약 사위 클래스가 원하는 객체를 직접 가져올 수 있다면 스스로 초기화가 가능하다.

```cpp
class Superpower
{
protected:
  void spawnParticles(ParticleType type, int count)
  {
    ParticleSystem& particles = Locator::getParticles();
    particles.spawn(type, count);
  }

  // Sandbox method and other operations...
};
```