---
title: "타입 객체"

categories:
  - GPP


tags:
  - gameprogrammingpattern
  - oop
  - typeobject

toc: true
toc_label: Type Object
toc_sticky: true

sidebar_main: true
---

클래스 하나를 인스턴스별로 다른 객체형으로 표현할 수 있게 만들어, 새로운 클래스들을 유연하게 만들 수 있도록 한다.

<br/>

# Motivation

용사를 쓰러뜨릴 포악한 몬스터를 구현하자. 몬스터는 다양한 리소스가 필요하지면 체력과 공격 속성만 고려하자.

모든 몬스터에는 체력 값이 있고 공격 문구 속성도 있다. 피해를 입으면 체력이 줄고, 유저를 때리면 공격 문구가 출력된다.

기획자는 용이나 트롤같이 다양한 몬스터 종족을 만들고 싶어한다. 각 종족은 몬스터의 특징이 된다. 종족은 몬스터의 최대 체력을 결정한다. 용은 트롤보다 체력이 높아 죽이기 훨씬 어렵고, 공격 속성도 정한다. 종족이 같은 몬스터는 공격 방식도 같다.

<br/>

## OOP 방식

전형적인 OOP 방식으로 코드를 짜보자. 용이나 트롤은 몬스터의 일종이닌까 우선 Base 클래스를 만든다.

```cpp
class Monster
{
public:
  virtual ~Monster() {}
  virtual const char* getAttack() = 0;

protected:
  Monster(int startingHealth)
  : health_(startingHealth)
  {}

private:
  int health_; // Current health.
};
```

getAttack 은 몬스터가 영웅을 공격할 때 보여줄 문구를 반환합니다. 하위 클래스에선 이걸 오버라이드해서 다른 공격 문구를 보여줄 계획이다.

이제 종족의 하위 클래스를 만들자.

```cpp
class Dragon : public Monster
{
public:
  Dragon() : Monster(230) {}

  virtual const char* getAttack()
  {
    return "The dragon breathes fire!";
  }
};

class Troll : public Monster
{
public:
  Troll() : Monster(48) {}

  virtual const char* getAttack()
  {
    return "The troll clubs you!";
  }
};
```

서브 클래스들은 몬스터의 최대 체력로 부모 클래스를 초기화하고 getAttack 을 오버라이드해서 알맞은 공격 문구를 반환한다. 아직까진 모든게 계획적이다. 이제 수많은 몬스터를 만들면 하위클래스가 정말 많아질것이다.

이쯤되면 작업이 이상하게 느려진다. 생산성이 왜 악화되는지 사례를 보자

1. 기획자로부터 트롤의 최대 체력을 48에서 52로 바꿔달라는 요청을 받는다
2. Troll.h 파일을 체크아웃한 뒤 수정한다.
3. 컴파일한다
4. 변경사항을 체크인한다.
5. 요청에 답장한다.
6. 1~5를 반복한다.

이쯤되면 프로그래머는 자신에게 좌절한다. 기획자가 숫자 몇개 바꾸려고 해도 하루 종일 걸린다. 종족 상태 값은 게임 코드 빌드 없이도 하루 변경할 수 있어야 한다. 더 나아가 프로그래머 도움 없이 기획자가 새로운 종족을 만들고 수정할 수 있어야 한다.

<br/>

## 클래스를 위한 클래스

앞에는 이 개념을 상속으로 구현했다. 용은 몬스터, 트롤도 몬스터 닌까 Monster 라는 추상 상위 클래스를 정의하고 하위 클래스에서 구현해 인스턴스를 만든다.

![to_oop](/assets/images/gameprogrammingpattern/to_oop.png)

이렇게 되면 몬스터의 종류가 많아질수록 상속 구조도 커진다. 종족이 늘어나면 코드를 추가하고 컴파일 해야한다.

다른 방법이 있다. 종족마다 정보를 몬스터에 두는 것이다. Monster 클래스를 상속받는 것이 아닌 Monster 클래스 하나와 Breed 클래스 하나만 만든다.

![to_oop_2](/assets/images/gameprogrammingpattern/to_oop_2.png)

이번엔 상속구조 없이 클래스 두 개 만으로 해결할 수 있다.

Breed 에는 종족이 같은 몬스터끼리 공유하는 정보인 최대 체력과 공격 문구가 들어있다.

모든 Monster 인스턴스는 종족 정보를 담고 있는 Breed 객체를 참조한다. 몬스터가 공격 문구를 얻을 때는 종족 객체 메소드를 호출한다. Breed 클래스는 몬스터 '타입'을 정의한다. 각각의 종족 객체는 개념적으로 다른 타입을 의미한다. 그래서 패턴 이름이 '타입 객체' 이다.

타입 객체 패턴의 장점은 코드 수정 없이도 새로운 타입을 정의할 수 있는게 장점이다. 코드에서 클래스 상속으로 만들던 것을 런타임에서 정의할 수 있는 데이터로 옮긴 셈이다.

새로운 Breed 인스턴스를 만들어서 새로운 종족을 계속 만들 수 있다. 설정 파일에서 읽은 데이터로 종족 객체를 만들게 한다면, 데이터 만으로도 새로운 종족을 만들 수 있는 셈이다. 이젠 기획자도 쉽게 새로운 몬스터를 만들 수 있게 되었다.

<br/>

## 패턴 

타입 객체 클래스와 타입 사용 객체 클래스를 정의한다. 모든 타입 객체 인스턴스는 논리적으로 모두 다른 타입을 의미한다. 타입 사용 객체는 자신의 타입을 나타내는 타입 객체를 참조한다.

인스턴스별로 다른 데이터는 타입 사용 객체 인스턴스에 저장하고, 개념적으로 같은 타입끼리 공유하는 데이터나 동작은 타입 객체에 저장한다.

<br/>

# 언제 쓸 것인가?

타입 객체 패턴은 다양한 종류를 정의해야 하 는데 개발 언어의 타입 시스템이 유연하지 않아 코드로 표햔하기 어려울 때 적합하다.

* 나중에 어떤 타입이 필요한지 알 수 없을 때
* 컴파일이나 코드 변경 없이 새로운 타입을 추가하거나 변경하고 싶을 때

<br/>

# 주의사항

유연한 데이터로 타입을 표현하는 것에 있다. 유연성을 얻는 것은 좋지만 타입을 코드가 아닌 데이터로 표현하면서 잃는 것도 있다.

<br/>

## 타입 객체를 직접 관리해야 한다

CPP 같은 타입 시스템은 컴파일러가 클래스를 위한 온갖 처리를 알아서 해준다는 장점이 있다. 각각의 클래스를 정의하는 데이터는 컴파일 될 때 자동으로 실행 파일의 정적 메모리 영역에 로드되어 동작한다.

타입 객체 패턴에서는 몬스터 인스턴스 뿐만 아니라 타입 객체도 직접 관리해야 한다. 타입 객체를 생성하고, 이것을 필요로 하는 몬스터가 있는 한 메모리에 로드되어 있어야 한다. 몬스터 인스턴스가 생성될 때 종족 객체 레퍼런스로 초기화 하는 것도 해야한다.

즉, 컴파일러가 해주던 일을 우리가 직접 구현해야 한다.

<br/>

## 타입별로 동작을 표현하기가 더 어렵다

상속 방식은 메소드 오버라이드를 통해 마음대로 할 수 있었다. 하지만 타입 객체 패턴은 Monster 클래스는 종족 객체 변수에 공격 문구를 저장하는 방식으로 표현해야 한다.

타입 객체로 타입 종속적인 데이터를 정의하기는 쉽지만  타입 종속적인 동작을 정의하기는 어렵다. 종족이 다른 몬스터마다 다른 AI 알고리즘을 적용하고 싶다면? 타입 객체로는 구현하기가 더더더더 어렵다.

이러한 한계를 우회하는 방법이 몇 가지 있다. 가장 쉬운 방법은 미리 동작 코드를 여러 개 만들어 놓고 타입 객체 데이터에서 이 중 하나를 선택하는 것이다. 이후 타입 객체가 적당한 함수 포인터를 저장하게 하면 타입 객체를 AI 알고리즘과 연께할 수 있다.

또한, 데이터만으로 동작을 정의할 수도 있다. 바이트 코드와 인터프리터 패턴을 이용해 동작을 표현하는 객체를 만들 수도 있다. 파일에서 데이터를 읽어 패턴으로 자료구조를 만들면 정의를 코드에서 데이터로 완전히 옮길 수 있다.

<br/>

# 예제 코드

우선 Breed 클래스부터 보자.

```cpp
class Breed
{
public:
  Breed(int health, const char* attack)
  : health_(health),
    attack_(attack)
  {}

  int getHealth() { return health_; }
  const char* getAttack() { return attack_; }

private:
  int health_; // Starting health.
  const char* attack_;
};
```

이 Breed 를 Monster 에서 어떻게 쓰는지 보자.

```cpp
class Monster
{
public:
  Monster(Breed& breed)
  : health_(breed.getHealth()),
    breed_(breed)
  {}

  const char* getAttack()
  {
    return breed_.getAttack();
  }

private:
  int    health_; // Current health.
  Breed& breed_;
};
```

Monster 클래스는 Breed 를 레퍼런스로 받고 이를 통해 받고 여기서 각종 설정을 포워딩해서 얻는다. 여기까지가 타입 객체 패턴이다.

<br/>

## 생성자 함수를 통해 타입 객체를 좀 더 타입처럼 만들기

앞에선 몬스터를 만들고 종족 객체도 직접 전달했다. 이런건 메모리를 먼저 할당하고 그 메모리에 클래스를 할당하는 것과 다를 게 없다. 대부분의 OOP에서는 이런식으로 객체를 만들지 않는다. 대신, 클래스의 생성자 함수를 호출해 클래스가 알아서 인스턴스를 생성하게 하자.

타입 객체에도 팩토리 메소드 패턴을 적용할 수 있다.

```cpp
class Breed
{
public:
  Monster* newMonster() { return new Monster(*this); }

  // Previous Breed code...
};

class Monster
{
  friend class Breed;

public:
  const char* getAttack() { return breed_.getAttack(); }

private:
  Monster(Breed& breed)
  : health_(breed.getHealth()),
    breed_(breed)
  {}

  int health_; // Current health.
  Breed& breed_;
};
```

Breed::newMonster 가 팩토리 메소드 패턴의 생성자이다. 이젠 몬스터를 이렇게 생성하면 된다.

```cpp
Monster* monster = someBreed.newMonster();
```

객체는 메모리 할당과 초기화 2단계로 생성된다. Monster 클래스 생성자 함수에서는 그래픽 로딩이나, AI 설정 등 필요한 모든 초기화 작업을 다 할 수 있다.

하지만 이런 초기화 작업은 메모리를 할당 한 이후에 진행된다. 아직 초기화도 안된 몬스터가 메모리에 올라가 있는 셈이다. 게임에서 객체 생성 과정을 제어하고 싶을 때 보통 커스텀 할당자나 객체 풀 패턴을 이용해 객체가 메모리 어디에 생성될 지를 제어한다.

Breed 클래스에 생성자 함수를 정의하면 이런 로직을 둘 곳이 생긴다. newMonster 를 호출하면 Monster 클래스에 초기화 제어권을 넘겨주기 전에 메모리 풀이나 커스텀 힙에서 메모리를 가져올 수 있다. 몬스터를 생성할 수 있는 유일한 곳인 Breed 안에 이런 로직을 두어 모든 몬스터가 정해놓은 메모리 관리 루틴을 강제할 수 있다.

<br/>

## 상속으로 데이터 공유하기

게임을 개발하다 보면 종족이 수백 개가 넘어가고 속성도 훨씬 많아질 것이다. 이럴 땐 종족을 통해 여러 몬스터가 속성을 공유한 것 처럼 여러 종족이 속성 값을 공유할 수 있게 만들면 좋겠다. 이번엔 프로그래밍 언어의 상속 기능이 아닌 타입 객체끼리 상속할 수 있는 시스템을 직접 구현한다.

간단하게 상속을 지원하자. 클래스가 상위 클래스를 갖는 것 처럼 종족 객체도 상위 종족 객체를 가질 수 있게 만든다.

```cpp
class Breed
{
public:
  Breed(Breed* parent, int health, const char* attack)
  : parent_(parent),
    health_(health),
    attack_(attack)
  {}

  int         getHealth();
  const char* getAttack();

private:
  Breed*      parent_;
  int         health_; // Starting health.
  const char* attack_;
};
```

Breed 를 만들 때 상속받을 종족 객체를 넘겨준다. 없다면 NULL을 넘긴다.

하위 객체에서는 어떤 속성을 상위에서 받을 지, 자기 값을 오버라이드할 지 제어할 수 있어야 한다. 여기선 최대 체력이 0이 아닐 때, 공격 문구가 NULL이 아닐 때는 자기 값을 쓰고 아니면 상위 객체 값을 쓰도록 하자.

```cpp
int Breed::getHealth()
{
  // Override.
  if (health_ != 0 || parent_ == NULL) return health_;

  // Inherit.
  return parent_->getHealth();
}

const char* Breed::getAttack()
{
  // Override.
  if (attack_ != NULL || parent_ == NULL) return attack_;

  // Inherit.
  return parent_->getAttack();
}
```

이 방법은 종족이 특정 속성 값을 더 이상 오버라이드 하지 않거나 상속받지 않도록 런타임에 바뀔 때 좋다. 다만, 상위 객체 포인터를 유지해야 하고 속성값을 반환할 때 마다 상위 객체를 확인하느라 더 느리다는 단점이 있다.

종족 속성 값이 바뀌지 않는다면 생성 시점에 바로 상속을 적용할 수 있다. 이런 것을 copy-down 위임이라고 한다.

```cpp
Breed(Breed* parent, int health, const char* attack)
: health_(health),
  attack_(attack)
{
  // Inherit non-overridden attributes.
  if (parent != NULL)
  {
    if (health == 0) health_ = parent->getHealth();
    if (attack == NULL) attack_ = parent->getAttack();
  }
}
```

이러면 상위 종족 객체를 포인터로 가지고 있지 않아도 되고, 속성 값을 반환할 땐 필드 값을 그대로 쓰면 된다.

```cpp
int         getHealth() { return health_; }
const char* getAttack() { return attack_; }
```

게임에서 JSON으로 종족을 정의한다고 해보자.

```json
{
  "Troll": {
    "health": 25,
    "attack": "The troll hits you!"
  },
  "Troll Archer": {
    "parent": "Troll",
    "health": 0,
    "attack": "The troll archer fires an arrow!"
  },
  "Troll Wizard": {
    "parent": "Troll",
    "health": 0,
    "attack": "The troll wizard casts a spell on you!"
  }
}
```

이 코드는 종족 데이터를 읽어서 새로운 종족 인스턴스를 만든다. "부모":"트롤" 필드에서 볼 수 있듯, 트롤 궁수와 트롤 마법사는 트롤 종족으로 부터 값을 상속받는다.

우리는 기획자가 자유롭게 제어할 수 있는 열린 시스템을 만들었다!

<br/>

# 디자인 결정

타입 객체 패턴에서는 프로그래밍 언어를 새로 만드는 것 처럼 타입 시스템을 맘대로 만들 수 있어, 설계의 폭이 넓고 재미있는 시도를 할 수 있다.

하지만, 현실적으로는 몇몇 이유 때문에 기능성이 많이 제한된다.

<br/>

## 타입 객체를 숨길것인가? 노출할것인가?

앞에선 Monster 가 Breed를 참조하지만 외부에 노출시키진 않아서 종족 객체를 직접 접근할 수가 없다. 외부에서는 몬스터에 따로 타입이 없어보인다.

물론 Breed를 반환하도록 할 수도 있다.

```cpp
class Monster
{
public:
  Breed& getBreed() { return breed_; }

  // Existing code...
};
```

이러면 Monster 설계가 바뀐다. 모든 몬스터에는 종족 객체가 있따는 사실이 공개 API에 포함된다. 둘 다 장단점이 있다.

<br/>

### 타입 객체를 캡슐화 하면

* 타입 객체 패턴의 복잡성이 나머지 다른 코드에는 드러나지 않는다
* 타입 사용 객체는 타입 객체로부터 동작을 선택적으로 오버라이드 할 수 있다
* 타입 객체 메소드를 전부 포워딩해야 한다

<br/>

### 타입 객체를 노출하면

* 타입 사용 클래스 인스턴스를 통하지 않고도 외부에서 타입 객체에 접근이 가능해진다
* 타입 객체가 공개 API의 일부가 되어버린다

<br/>

## 타입 사용 객체를 어떻게 생성할 것인가?

### 객체를 생선한 뒤에 타입 객체를 넘겨주는 경우

* 외부 코드에서 메모리 할당을 제어할 수 있다

<br/>

### 타입 객체의 '생성자' 함수를 호출하는 경우

* 타입 객체에서 메모리 할당을 제어한다

<br/>

## 타입을 바꿀 수 있는가?

지금까진 한번 만들어진 객체는 절대 바뀌지 않는다 가정했다. 꼭 그래야 하진 않는다. 객체가 필요하면 타입을 바뀌게 할 수 있다.

<br/>

### 타입을 바꿀 수 없다면

* 코드를 구현하고 이해하기가 더 쉽다
* 디버깅하기 쉬받

<br/>

### 타입을 바꿀 수 있다면

* 객체 생성 횟수가 줄어든다
* 요구사항이 깨지지 않도록 주의해야 한다

<br/>

## 상속을 어떻게 지원할 것인가?

### 상속 없음

* 단순하다
* 중복 작업을 해야할 수 있다

<br/>

### 단일 상속

* 그나마 단순하다
* 속성 값을 얻는데 오래걸린다

<br/>

### 다중 상속

* 거의 모든 데이터 중복을 피할 수 있다.
* 많이 복잡하다. 구글 코딩 표준에서는 인터페이스를 제외하곤 다중 상속을 금지한다.

<br/>

