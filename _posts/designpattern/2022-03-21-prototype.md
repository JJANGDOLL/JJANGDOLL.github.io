---
title: "프로토타입 패턴"

categories:
  - DESIGNPATTERN


tags:
  - designpattern
  - oop
  - prototype

toc: true
toc_label: Prototype
toc_sticky: true

sidebar_main: true
---

새로운 객체를 생성하지 않고 기존의 객체를 복사합니다. 복제를 통해 자원을 절약합니다.

<br/>

# 객체 복제

객체를 생성하는 방법은 new 키워드를 사용하는 것 입니다. 하지만 기존의 객체를 복제해서 생성하는 방법도 있습니다. 프로토타입 패턴은 기존의 객체를 복제해서 객체를 생성합니다.

```cpp
class Hello
{
public:
    std::string Message;

    Hello(std::string InMsg)
    {
        Message = InMsg;
    }

    ~Hello()
    {
        cout << "Destructor" << endl;
    }

    void ShowData()
    {
        cout << Message << endl;
    }

    void SetMsg(std::string InMsg)
    {
        Message = InMsg;
    }
};

void main()
{
    Hello hello = Hello("Hello");
    hello.ShowData();

    Hello hello2 = hello;
    hello2.ShowData();

    return;
}
```

이런 방식으로 새로운 객체를 생성하면 생성자의 호출에 필요한 자원을 아낄 수 있습니다. 이처럼, 복자 생성자를 사용해서 새로운 객체를 생성할 수 있습니다.

<br/>

## 얕은 복사

```cpp
class ShallowCopy
{
public:
    int* Num;

    ShallowCopy(int InNum)
    {
        Num = new int;
        *Num = InNum;
    }

    void SetNum(int InNum)
    {
        *Num = InNum;
    }

    void ShowData()
    {
        cout << *Num << endl;
    }
};

void main()
{
    ShallowCopy shallow = ShallowCopy(5);
    shallow.ShowData();

    ShallowCopy shallow2 = shallow;
    shallow2.ShowData();

    shallow2.SetNum(10);

    shallow.ShowData();
    shallow2.ShowData();

    return;
}
```

얕은 복사(Shallow copy) 는 객체를 공유하는 방식으로 복사합니다. 객체를 복사할 때 새로운 자원을 할당받지 않고 객체를 복사합니다. 위 코드를 살펴보면 `ShallowCopy::Num` 의 주소는 추가로 자원이 할당되지 않고 두 클래스가 공유하고 있습니다.

<br/>

## 깊은 복사

```cpp
class DeepCopy
{
public:
    int* Num;

    DeepCopy(int InNum)
    {
        Num = new int;
        *Num = InNum;
    }

    DeepCopy(const DeepCopy& data)
    {
        Num = new int;
        *Num = *(data.Num);
    }

    void SetNum(int InNum)
    {
        *Num = InNum;
    }

    void ShowData()
    {
        cout << *Num << endl;
    }
};

void main()
{
    DeepCopy deep = DeepCopy(5);
    deep.ShowData();

    DeepCopy deep2 = deep;
    deep2.ShowData();

    deep2.SetNum(10);

    deep.ShowData();
    deep2.ShowData();

    return;
}
```

깊은 복사(DeepCopy)는 객체를 물리적으로 다른 메모리에 복제합니다. CPP 에서 깊은 복사를 하기 위해선 클래스의 복사 생성자를 오버라이딩 해야합니다.

<br/>

# 프로토타입

프로토타입 패턴은 신규 객체를 생성하지 않고 기존 객체를 복제합니다. 기존 객체를 재사용하면 신규 객체를 생성하는 것 보다 자원이 절약됩니다.

프로토타입의 사전적 정의는 `원형, 기초, 표준` 입니다. 객체를 복제하기 위해선 먼저 생성된 객체가 있어야 하고 이 복제 대상이 되는 원본 객체를 원형이라고 합니다.

```cpp
class Prototype
{
public:
    int* Num;

    Prototype(int InNum)
    {
        Num = new int;
        *Num = InNum;
    }

    Prototype(const Prototype& data)
    {
        Num = new int;
        *Num = *(data.Num);
    }

    ~Prototype()
    {

    }

    Prototype* Clone() const
    {
        return new Prototype(*this);
    }

    void SetData(int InNum)
    {
        *Num = InNum;
    }
    
    void ShowData() const
    {
        cout << *Num << endl;
    }
};

void main()
{
    Prototype* prototype = new Prototype(5);
    prototype->ShowData();

    Prototype* prototype2 = prototype->Clone();
    prototype2->ShowData();

    prototype2->SetData(10);

    prototype->ShowData();
    prototype2->ShowData();

    return;
}
```
