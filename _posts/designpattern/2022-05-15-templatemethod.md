---
title: "템플릿 메소드 패턴"

categories:
  - DESIGNPATTERN


tags:
  - designpattern
  - oop
  - template method

toc: true
toc_label: Template Method
toc_sticky: true

sidebar_main: true
---

객체의 행위를 단계적으로 분리합니다. 추상화를 통해 공통된 단계와 공통되지 않은 단계를 분리함으로써 다양한 로직을 처리합니다.

<br/>

# 프로그램의 구조

모든 행동에는 순서가 있습니다. 프로그램은 이렇나 동작을 분석해서 순차적으로 연결합니다.

프로그래밍은 사용자가 원하는 동작을 정확히 이해하고 분석하는 것 부터 시작합니다. 이 분석 결과를 코드로 작성합니다.

예를 들어 샌드위치 제작 순서를 분석하면 `2개의 빵 사이에 원하는 재료를 넣습니다.` 처럼 됩니다.

```cpp
class Sandwich
{
public:
    std::string Make()
    {
        return "빵 + 속재료 + 빵";
    }
};
```

샌드위치에 대한 설명이 너무 추상적인듯 합니다. 실제 샌드위치만드는 과정은 훨씬 복잡하닌까요

이 정도의 설명으로도 사람은 원하는 샌드위치를 잘 만듭니다. 인간은 어떤 행동을 추상화하고 이를 이해하기 때문에 가능합니다.

하지만 컴퓨터를 이해시키기 위해 코딩하기 위해선 훨씬 구체적인 동작을 기술해야합니다. 동작 처리 과정을 보다 면밀하게 구분하고 구체적으로 나열합니다.

컴퓨터는 동작을 하나씩 지정해서 실행하므로 컴퓨터 프로그래밍을 바르게 작성하는 것은 각각의 단계를 잘 구별하는 것이라고 할 수 있습니다.

```cpp
class Sandwich
{
public:
    std::string Make()
    {
        std::string sandwich_;

        sandwich_ += "빵";

        sandwich_ += "+";
        sandwich_ += "속재료";

        sandwich_ += "+";
        sandwich_ += "빵";

        return sandwich_;
    }
};
```

샌드위치 생성 과정을 단계별로 구분했습니다. 코드는 길어졌지만 결과는 동일합니다.

<br/>

# 템플릿

샌드위치는 공통된 모양의 형태를 가지고 만드는 원리와 만들어진 모습이 같습니다. 속재료만 다를뿐 겉모양은 비슷합니다.

여기서는 공통된 특징을 이용해 분리합니다.

```cpp
class Sandwich
{
public:
    std::string Make()
    {
        std::string sandwich_;

        sandwich_ += bread();

        sandwich_ += "+";
        sandwich_ += jam();

        sandwich_ += "+";
        sandwich_ += bread();

        return sandwich_;
    }

    std::string bread()
    {
        return "식빵";
    }

    std::string jam()
    {
        return "딸기잼";
    }
};
```

식빵을 만들기 위한 재료들을 메소드로 분리했습니다. make 에서는 재료에 해당하는 함수를 호출하지만 과정은 변함이 없습니다.

<br/>

## 템플릿 이란

템플릿에는 '견본', '틀' 이라는 뜻이 있고, OOP에서 템플릿은 공통된 처리 로직을 의미합니다.

우리는 샌드위치를 만들기 위한 공통 로직을 설계했고 make 메소드를 반복적으로 실행하면 어떤 샌드위치든 찍어낼 수 있습니다. make 처럼 공통된 단계 과정을 템플릿이라고 합니다.

<br/>

# 일반화

일반화(generalization)는 공통점을 찾아 상위 클래스로 도출하는 과정입니다. 공통점을 기준으로 1개의 클래스를 2개의 클래스로 분리합니다.

일반화는 OOP의 상속을 구현하기 위해 공통된 부분을 찾는 과정입니다. 다양한 클래스로 객체를 확장할 때 공통된 부분만 모아서 별도로 관리하면 추후 유지보수가 용이합니다.

일반화를 통해 공통된 로직을 상위 클래스로 분리했으므로, 적절하게 접근 지정자를 사용하여 템플릿 접근만 허용합니다.

```cpp
class Sandwich
{
protected:
    std::string Make()
    {
        std::string sandwich_;

        sandwich_ += bread();

        sandwich_ += "+";
        sandwich_ += jam();

        sandwich_ += "+";
        sandwich_ += bread();

        return sandwich_;
    }

    virtual std::string bread();
    virtual std::string jam();
};

class Strawberry : public Sandwich
{
    std::string bread()
    {
        return "식빵";
    }

    std::string jam()
    {
        return "딸기잼";
    }
};

class Peanutbutter: public Sandwich
{
    std::string bread()
    {
        return "식빵";
    }

    std::string jam()
    {
        return "땅콩잼";
    }
};
```

