---
title: "팩토리 메소드 패턴"

categories:
  - DESIGNPATTERN


tags:
  - designpattern
  - oop
  - factory method pattern

toc: true
toc_label: Factory Method pattern
toc_sticky: true

sidebar_main: true
---

팩토리를 확장한 패턴입니다. 팩토리 패턴에 추상화를 결합하여 객체의 생성과 사용을 분리합니다. 별도로 준비한 추상 메소드에 생성을 위임합니다.

<br/>

# Abstract

팩토리 메소드는 추상화 기법을 사용하여 패턴을 확장합니다.

## 추상화란?

우리가 복잡한 모듈을 분석할 때 처음부터 세부적인 부분 하나 하나 면밀히 살펴보지는 않습니다. 만약 파악하려는 기능이 요약된 정보가 있다면 보다 쉽게 이해할 수 있습니다. 객체에 추상적 개념을 적용하는 이유는 동작을 보다 쉽게 파악하기 위해서입니다. 

추상 클래스를 생성하기 위해선 클래스 내부에 순수 가상함수가 한개 이상 존재하면 됩니다.

```cpp
class Factory
{
public:
    virtual void Test() = 0;
};
```

<br/>

# 팩토리 메소드 패턴

아래 같은 클래스를 생성했다고 가정해봅시다.

```cpp
class LgProduct
{
public:
    std::string name()
    {
        return "LG Gram Laptop";
    }
};
```

이 클래스를 인스턴스 하기 위한 팩토리 패턴을 구현할 수 있습니다.

```cpp
class Factory
{
public:
    LgProduct* create()
    {
        return new LgProduct();
    }
};

int main()
{
    Factory* factory = new Factory();
    LgProduct* lgLaptop = factory->create();

    std::cout << lgLaptop->name() << std::endl;
}

```
<br/>


## 추상화

이제 팩토리 패턴에 추상화 적용하고 패턴을 확장해봅니다. 추상화를 적용하게 되면 팩토리 메소드 패턴과 패곹리 패턴 2가지 형태로 구분되게 됩니다.

```cpp
class Factory
{
public:
    LgProduct* create()
    {
        return this->createProduct();
    }

    virtual LgProduct* createProduct() = 0;
};
```

Factory 클래스에 추상화를 적용해보았습니다. 

추상클래스는 순수 가상함수가 있어 인스턴스 할 수 없습니다. 그렇다면 추상 클래스를 상속받아 실제로 인스턴스 가능한 클래스를 만들어야 합니다.

```cpp
class ProductFactory: public Factory
{

public:
    LgProduct* createProduct() override
    {
        return new LgProduct();
    }
};
```

이후 ProductFactory 를 인스턴스 해서 사용할 수 있습니다.

```cpp
int main()
{
    Factory* factory = new ProductFactory();
    LgProduct* lgLaptop = factory->create();

    std::cout << lgLaptop->name() << std::endl;
}
```

<br/>

## 상품 추가

LgProduct 말고 SamsungProduct 를 추가한다고 가정해봅시다.

```cpp
class SamsungProduct
{
public:
    std::string name()
    {
        return "Samsung Galaxy Laptop";
    }
};
```

이 SamsungProduct 클래스도 ProductFactory 에서 인스턴스 해봅시다.

```cpp
enum COMPANY
{
    LG,
    SAMSUNG
};

class Product
{
public:
    virtual std::string name() = 0;
};

class LgProduct : public Product
{
public:
    std::string name()
    {
        return "LG Gram Laptop";
    }
};

class SamsungProduct : public Product
{
public:
    std::string name()
    {
        return "Samsung Galaxy Laptop";
    }
};

class Factory
{
public:
    Product* create(COMPANY company)
    {
        return this->createProduct(company);
    }

    virtual Product* createProduct(COMPANY) = 0;
};

class ProductFactory: public Factory
{

public:
    Product* createProduct(COMPANY company) override
    {
        if(company == COMPANY::LG)
            return new LgProduct();
        else if(company == COMPANY::SAMSUNG)
            return new SamsungProduct();
    }
};

int main()
{
    Factory* factory = new ProductFactory();
    Product* lgLaptop = factory->create(COMPANY::LG);
    std::cout << lgLaptop->name() << std::endl;
    Product* samsungLaptop = factory->create(COMPANY::SAMSUNG);
    std::cout << samsungLaptop->name() << std::endl;
}
```

이렇게 인스턴스와 관련된 모든 로직을 한 클래스에서 집중되게 할 수 있습니다. 수정이 필요한 경우 자식 클래스에서만 수정을 할 수 있어 유지 보수가 용이합니다.

