---
title: "장식자 패턴"

categories:
  - DESIGNPATTERN


tags:
  - designpattern
  - oop
  - decorator

toc: true
toc_label: Decorator
toc_sticky: true

sidebar_main: true
---

객체에 동적 기능을 추가하기 위해 구조를 개선하는 패턴입니다. 다양한 확장을 위해 객체를 조합합니다.

<br/>

# 객체의 확장

새로운 기능을 추가하기 위해 객체를 확장하는 방법은 **상속** 과 **구성** 두 가지 입니다.



<br/>

## 상속

상속(Inheritance)은 기존 객체를 확장해 새로운 기능을 확장하는 방법입니다. 부모 자식 간의 강력한 결합이 형성되어 유연한 확장이 어렵습니다. `is ~a` 관계를 가집니다.

`override` 는 상위 클래스의 메소드를 재정의 하는 기능입니다. 다만, 상위 메소드를 재정의 해서 하위 메소드로 대체 될 뿐이며 기존의 상위 메소드가 사라지는 것은 아니며 불필요한 상위 메소드가 남을 수 있으며 클래스 가 점점 커지게 됩니다.

아래 표 처럼 원하는 기능만 상속받고 싶은 경우에도 매우 복잡해집니다.

| 기능 | 옵션1 | 옵션2 | 옵션3 | 옵션 4 |
|------|:--:|:--:|:--:|:--:|:--:|
|기능1|O| |O |O |
|기능2|O|O||O |
|기능3| |O||O |
|기능4| ||O|O||

<br/>

## 구성

구성(Composition)은 다른 객체를 멤버로 가져 기능을 확장하는 방법입니다. 의존성 주입된 클래스의 기능의 동작을 변경할 수는 없습니다. `has ~a` 관계를 가집니다.

다양한 행위를 구분하기 위해 조건을 추가해 분기를 태울 수 있습니다. 분기를 나눈다는 점에서 기존의 행위를 확장하는 것과 유사합니다. 

구성은 위임을 활용해 객체를 확장하는 방법입니다. 장식자 패턴은 구성을 통해 확장합니다.

<br/>

### OCP

객체를 확장할 때 에는 개방-폐쇄 원칙(Open-closed principle)을 기반으로 합니다. OCP는 새로운 기능을 추가할 경우 확장은 허용하지만 기존 내용은 변경하지 못하게 하는 원칙입니다. OOP 에서는 OCP 원칙을 지키기 위해 구성을 사용합니다. 구성을 사용하면 기존 코드를 변경하지 않고도 객체에 새로운 기능을 추가할 수 있습니다.

<br/>

# 장식자

장식자 패턴은 객체에 새로운 부가 기능을 동적으로 추가합니다. 여기서 동적은 실시간으로 변경하는 객체에 새로운 행위를 추가하는 것을 의미합니다.

<br/>

## 래퍼

장식자는 기본 객체를 감싸서 새로운 객체로 확장합니다. 마치 랩으로 감싸는 것과 같다고 해서 래퍼 객체라고도 합니다. 데코레이터는 기존 객체를 다른 행위로 꾸미는(Decorate)하는 형태로 보이므로 장식이라는 말로 불립니다.

![DecorateRapper](/assets/images/designpattern/decorate_rapper.png)

<br/>

## 투명성

장식자는 객체를 감싸면서 또 다른 객체로 파생하며 확장된 객체에도 동일한 인터페이스를 적용합니다. 클라이언트에서는 객체가 원본 객체인지 파생된 객체인지 알 수 없습니다. 이처럼 동일한 인터페이스를 사용해 객체의 투명성을 부여합니다.

![DecorateTransparnet](/assets/images/designpattern/decorate_transparent.png)


<br/>

## 구성

장식자 패턴은 4개의 구성 요소로 이루어져있습니다.

* Component
* ConcreateComponent
* Decorator
* ConcreateDecorator

<br/>

## 설계

장식자 패턴에서 먼저 설계할 것은 Component 입니다. Component 는 공통 기능을 정의하는 인터페이스입니다.

```cpp
class Component
{
public:
    virtual std::string Name() = 0;
    virtual int Price() = 0;
};

class Product : public Component
{
public:
    virtual std::string Name() override
    {
        return "원피스";
    }

    virtual int Price() override
    {
        return 20000;
    }
};
```

장식자 패턴을 적용하기 위해 컴포넌트와 동일한 인터페이스를 적용합니다.

```cpp
class Decorate: public Component
{
public:
    Component* comp;

public:
    Decorate(Component *InComp)
    {
        comp = InComp;
    }

    virtual std::string Name() = 0;
    virtual int Price() = 0;
};


class i7: public Decorate
{
public:
    i7(Component* InComp):Decorate(InComp) {};
    virtual std::string Name() override
    {
        return comp->Name() + ", i7";
    }

    virtual int Price() override
    {
        return comp->Price() + 435000;
    }
};

class ssd256: public Decorate
{
public:
    ssd256(Component* InComp):Decorate(InComp) {};
    virtual std::string Name() override
    {
        return comp->Name() + ", ssd256";
    }

    virtual int Price() override
    {
        return comp->Price() + 128000;
    }
};

int main(void)
{
    Component* d = new ssd256(new i7(new Product()));

    cout << d->Name() << endl;
    cout << d->Price() << endl;
}
```