---
title: "추상 팩토리 패턴"

categories:
  - DESIGNPATTERN


tags:
  - designpattern
  - oop
  - abstract factory pattern

toc: true
toc_label: Abstract Factory
toc_sticky: true

sidebar_main: true
---

팩토리 메소드를 확장한 패턴입니다. 팩토리 메소드보다 좀 더 큰 그룹 단위의 객체 관리를 합니다. 팩토리에 인터페이스를 적용하여 객체를 생성합니다.

<br/>

# 추상 팩토리

추상 팩토리는 여러 개의 팩토리 메소드를 그룹으로 묶은 것과 유사합니다. 따라서 팩토리 메소드에 대해 미리 알고있어야 합니다.

```cpp
class Factory
{
public:
    virtual void createTire() = 0;
    virtual void createDoor() = 0;
};
```

자동차 생성 과정을 처리하는 클래스를 만들었습니다.

여기서 팩토리 메소드는 추상 클래스와 자식 클래스를 1개로만 구성합니다. 하지만 추상 팩토리는 다형성을 적극 활용해서 다수의 하위 클래스를 생성하고 관리합니다.

<br/>

## 팩토리 그룹

추상 팩토리의 특징은 추상화를 이용해 하위 클래스을 그룹으로 만들 수 있습니다. 

위 Factory 클래스를 상속받아 한국 공장과 미국 공장을 만들어 공장 그룹을 관리해봅시다. 

<br/>

### 한국 공장

Factory 를 상속받아 한국 공장을 만들어봅시다.


```cpp
class KoreaFactory: public Factory
{
public:
    KoreaFactory()
    {
        cout << "한국 공장 생성" << endl;
    }

    void createTire() override
    {
        cout << "한국 타이어 생성" << endl;
    }

    void createDoor() override
    {
        cout << "한국 문 생성" << endl;
    }
};
```

<br/>

### 미국 공장

```cpp
class USFactory: public Factory
{
public:
    USFactory()
    {
        cout << "미국 공장 생성" << endl;
    }

    void createTire() override
    {
        cout << "미국 타이어 생성" << endl;
    }

    void createDoor() override
    {
        cout << "미국 문 생성" << endl;
    }
};
```

<br/>

## 은닉성

하위 클래스는 서로 독립적인 그룹으로 실제 구현부를 외부로부터 감출 수 있습니다. 이러한 객체지향적 특징을 은닉성 이라고 합니다.

<br/>

## 부품 확장

위에서 Factory 만든 클래스를 바탕으로 각 나라에서 생산되는 부품마다 추가 작업이 필요하다는 요청을 처리해봅시다.

<br/>

### 각 부품의 추상화

각 부품의 추상 클래스를 생성해서 하위 클래스에서 구현합니다.

```cpp
class TireProduct
{
public:
    virtual void makeAssemble() = 0;
};

class KoreaTireProduct : public TireProduct
{

public:
    void makeAssemble() override
    {
        cout << "한국 타이어 생산 및 조립" << endl;
    }
};

class USTireProduct: public TireProduct
{

public:
    void makeAssemble() override
    {
        cout << "미국 타이어 생산 및 조립" << endl;
    }
};

class DoorProduct
{
public:
    virtual void makeAssemble() = 0;
};

class KoreaDoorProduct: public DoorProduct
{

public:
    void makeAssemble() override
    {
        cout << "한국 문 생산 및 조립" << endl;
    }
};

class USDoorProduct: public DoorProduct
{

public:
    void makeAssemble() override
    {
        cout << "미국 문 생산 및 조립" << endl;
    }
};
```

각 국가별로 다른 상품을 적용할 수 있게 하위 클래스를 선언하고 공장에서는 각자 원하는 Product 를 생성하도록 합니다.

```cpp
class KoreaFactory: public Factory
{
public:
    KoreaFactory()
    {
        cout << "한국 공장 생성" << endl;
    }

    void createTire() override
    {
        (new KoreaTireProduct())->makeAssemble();
    }

    void createDoor() override
    {
        (new KoreaDoorProduct())->makeAssemble();
    }
};

class USFactory: public Factory
{
public:
    USFactory()
    {
        cout << "미국 공장 생성" << endl;
    }

    void createTire() override
    {
        (new USTireProduct())->makeAssemble();
    }

    void createDoor() override
    {
        (new USDoorProduct())->makeAssemble();
    }
};
```

<br/>

## 장점

* 생성 패턴을 독립적으로 동작하도록 분리해서 하나의 그룹으로 묶고 원하는 객체를 선택해서 관리합니다.
* 추상 팩토리를 상속받았기에 동일한 처리 로직을 가지고 있습니다.

<br/>

## 단점

* 하위 클래스를 추가하기가 어렵습니다.
* 위처럼 새로운 부품이 추가되면 수정이 많이 필요할 수도 있습니다.
