---
title: "플라이웨이트 패턴"

categories:
  - DESIGNPATTERN


tags:
  - designpattern
  - oop
  - flyweight

toc: true
toc_label: FlyWeight
toc_sticky: true

sidebar_main: true
---

객체를 공유합니다. 객체 공유를 통해 자원의 효율성과 공유된 객체의 일관성을 보장합니다.

<br/>

# 메모리 자원

메모리를 효율적 사용하는 건 프로그래머의 숙원사업입니다. 그렇기에 중복을 제거하고 재사용을 더 많이 하도록 꾸준히 수정합니다.

<br/>

## 단일 책임 원칙

SRP(Single Reponsibility Principle : 단일 책임 원칙)는 클래스를 설계할 때 객체가 하나의 책임만 갖도록 하는 원칙입니다. 하나의 객체가 다양한 기능과 책임을 가지게 되면 관리가 어렵습니다.

<br/>

## 인삿말

```cpp
class Platform
{
public:
    std::string Console(std::string msg)
    {
        return msg + "\n";
    }

    std::string Browser(std::string msg)
    {
        return msg + "<br/>";
    }
};

class Korean : public Platform
{
public:
    std::string Greeting()
    {
        return this->Console("안녕하세요");
    }
};

class English : public Platform
{
public:
    std::string Greeting()
    {
        return this->Console("Hello");
    }
};
```

이 코드는 단순한 인삿말을 출력하지만, 플랫폼에 따라 개행 문자(carriage return)을 달리합니다. 사용에는 크게 문제가 없지만, 상속한  객체는 독립적인 객체로 생성되기에 메모리를 살펴보면 중복이 발생합니다.


![Flywei_dup](/assets/images/designpattern/flywei_dup.png)

따라서, 규모가 점점 커진다면 해결해야 할 문제로 남아있게 됩니다.

효율적인 자원 사용을 위해선 최대한 중복을 지양해야 합니다. 플라이 웨이트 패턴은 중복을 제거하고 공유를 통해 자원을 효율적으로 사용합니다.

<br/>

# 플라이 웨이트

플라이 웨이트는 Fly(가벼운) Weight(질량)의 합성어입니다. 가벼운 객체란 메모리를 적게 소모하는 객체를 말합니다. 중복되는 코드의 객체를 공유해서 메모리 할당을 줄이고 중복된 객체를 상속하거나 생성하지 않고 자원을 재사용해서 효율을 개선합니다.

<br/>

## 자원 공유

인삿말 클래스를 수정해서 중복을 제거해봅니다.

```cpp
class Platform
{
public:
    std::string Console(std::string msg)
    {
        return msg + "\n";
    }

    std::string Browser(std::string msg)
    {
        return msg + "<br/>";
    }
};

class Korean
{
private:
    Platform& _platform;

public:
    Korean(Platform platform): _platform(platform) {};

    std::string Greeting()
    {
        return _platform.Console("안녕하세요");
    }
};

class English
{
private:
    Platform& _platform;

public:
    English(Platform platform): _platform(platform) {};

    std::string Greeting()
    {
        return _platform.Console("Hello");
    }
};
```

상속이 아닌 의존성 주입을 통해 Platform 클래스의 객체가 중복으로 생성되는 것을 방지해서 메모리를 절약합니다.

플라이 웨이트 패턴은 다양한 생성자 패턴과 함께 조합해서 활용성을 높일 수 있습니다.
