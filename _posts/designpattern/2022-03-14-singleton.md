---
title: "싱글톤 패턴"

categories:
  - DESIGNPATTERN


tags:
  - designpattern
  - oop
  - singleton pattern

toc: true
toc_label: Singleton pattern
toc_sticky: true

sidebar_main: true
---

객체 생성 개수를 1개로 제한합니다.

<br/>

# 객체의 생성

클래스는 선언 뿐 아니라 인스턴스 해서 객체로 생성 해야합니다. 이것은 OOP의 원리이자 특징이며 클래스를 붕어빵 틀, 객체를 붕어빵이라고 비유하는 이유입니다.

각각의 오브젝트는 독립된 리소스 입니다. 즉, 서로 다른 메모리를 차지해서 상호간에 영향을 주지 않는 다는 얘기입니다.

![Resourceshare](/assets/images/designpattern/resourceshare.png)

하지만 객체의 상태를 공유하고 싶으면 어떤 방법을 사용해야 할까요? 

<br/>

## 전역변수

하나의 객체를 공유하기 위한 방법으로는 전역 변수를 사용하는 방법이 있습니다.

```cpp
#include <iostream>
#include <string>

class Hello
{
public:
    std::string Greeting()
    {
        return "Hello";
    }
};

Hello* hello;

int main()
{
    hello = new Hello();
    std::cout << hello->Greeting() << std::endl;
}
```

하지만 전역변수는 다양한 위험성을 내포하고 있습니다.

<br/>

# 싱글턴

생성한 객체를 공유하려면 하나의 객체만 존재해야 합니다. new 할당마다 독립적인 객체를 계속 생성해준다면 서로 공유할 수 없습니다. 싱글턴은 다른 생성 패턴과는 다르게 하나의 객체로만 생성을 제한하는 패턴입니다. 

<br/>

## 유일한 객체

응용 프로그램에서 공용 장치 처럼 하나의 객체만 필요한 경우가 많습니다. 여러 대의 프린터가 연결되도 프린터로 전송하는 데이터 스풀은 하나입니다. 또한 프로그램의 환경 설정 파일도 하나만 있습니다.

이런 것이 여러개 존재한다면 시스템과 프로그램은 자주 충돌할 것입니다. 충돌을 방지하려면 단일 객체를 사용해야 하며, 싱글턴은 하나의 객체만 유지하도록 하는 생성 패턴입니다. 싱글턴 패턴은 다음상황에 유용합니다.

* 공유 자원 접근
* 복수의 시스템이 하나의 자원에 접근할 때
* 유일한 객체가 필요할 때
* 값의 캐시가 필요할 때

<br/>

## 설계

new 키워드는 객체를 생성할 때 클래스의 생성자를 호출합니다. 하지만 싱글턴의 경우 생성을 제한하기 위해 접근 지정자를 private 로 제한해야 합니다. 또한 여기서 클래스가 복사되서 클론이 되는 것을 막아야 합니다. 결과적으로 생성자와 복사 생성자를 제한해야 합니다. 

그렇다면 인스턴스는 어떻게 해야 할까요? 내부적으로 정적(static) 메소드를 생성해서 인스턴스를 진행합니다. 

```cpp
class Hello
{
public:
    static Hello& GetInstance()
    {
        static Hello singleton;
        return singleton;
    }

    std::string Greeting() { return "Hello"; }

private:
    Hello() {};
    ~Hello() {};
    Hello(const Hello&);
    Hello& operator=(const Hello&);
};

int main()
{
    Hello& hello = Hello::GetInstance();

    std::cout << hello.Greeting() << std::endl;
}
```

<br/>
