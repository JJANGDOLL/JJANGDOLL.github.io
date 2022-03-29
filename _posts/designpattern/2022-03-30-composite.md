---
title: "복합체 패턴"

categories:
  - DESIGNPATTERN


tags:
  - designpattern
  - oop
  - composite

toc: true
toc_label: Composite
toc_sticky: true

sidebar_main: true
---

객체 간의 계층적 구조화를 통해 객체를 확장합니다. 복합체는 재귀적으로 결합된 트리 구조의 객체입니다.

<br/>

# 복합 객체

복합 객체는 객체가 또 다른 객체를 포함하는 것을 의미합니다. 복합적인 객체 관계를 복합화(composition), 집단화(Aggregation) 이라고 합니다.

객체의 복합화는 객체를 더 큰 구조로 확장하면서도 상속과 다르게 느슨한 결합을 가지고 있으며, 의존체 주입을 사용합니다.

<br/>

## 컴퓨터

```cpp
class Monitor
{
public:
    std::string name = "모니터";
};

class Computer
{
public:
    std::string Name = "구성";
    Monitor& monitor;
    
public:
    Computer(Monitor& InMonitor): monitor(InMonitor) {};
};

int main(void)
{
    Monitor monitor;
    Computer computer(monitor);
}
```

Computer 클래스는 Monitor 객체를 가지고 있는 복합 객체가 되었습니다.

</br>

## 객체 확장

### 수평 확장

복합 객체 특징은 다른 객체 정보를 포함하면서 수평적으로 확장됩니다. 객체는 여러 개의 정보를 동시에 가질 수 있습니다.

Computer 클래스에 Disk와 Memory 를 추가합니다.

```cpp
class Memory
{
public:
    std::string name = "메모리";
};

class Disk
{
public:
    std::string name = "디스크";
};

...

class Computer
{
public:
    std::string Name = "구성";
    Monitor& monitor;
    Memory& memory;
    Disk& disk;
    
public:
    Computer(Monitor& InMonitor, Memory& InMemory, Disk& InDisk): monitor(InMonitor), memory(InMemory), disk(InDisk) {};
};
```

<br/>

### 수직 확장

복합 객체를 자식 객체로 사용할 때는 수직적 확장 구조를 가집니다. 복합 객체는 수직적 확장을 통해 계층적이고 복잡한 트리 구조를 띄게 됩니다.

Monitor 클래스를 확장시켜 복합 객체로 만들어봅니다.

```cpp
...

class Screen
{
public:
    std::string name = "32인치";
};

class Monitor
{
public:
    std::string name = "모니터";
    std::vector<Screen*> screens;

public:
    void addMonitor(Screen* InScreen)
    {
        screens.push_back(InScreen);
    }

    void Show()
    {
        for(Screen* screen : screens)
        {
            cout << screen->name << endl;
        }
    }
};

...

int main(void)
{
    Monitor monitor;
    monitor.addMonitor(new Screen());
    monitor.addMonitor(new Screen());
    Disk disk;
    Memory memory;
    Computer computer(monitor, memory, disk);
    computer.monitor.Show();
}
```

결과적으로 다음과 같은 구조를 가지게 됩니다.

![Composite_extend](/assets/images/designpattern/composite_extend.png)

<br/>

## 특징

### 재귀적 결합

복합 객체는 서브 하위 객체를 그룹화하는 특징이 있습니다. 객체 그룹화를 통해 더 큰 집단의 객체로 확장하는 것을 집단화 라고 합니다.

트리 모양에서 제일 마지막 노드를 leaf, 중간에서 다시 확장하는 객체를 node 라고 합니다.

![Composite_extend](/assets/images/designpattern/composite_tree.png)


<br/>

### 구성 요소

복합체는 하나의 구조 안에 또 다른 구조를 가진 모델, 주로 파일 탐색이나 조직도를 그릴  설계할 때 많이 사용됩니다.

복합체 패턴은 크게 4가지 구성 요소로 이루어져있습니다.

* Component
* Composite
* Leaf
* Client

<br/>

## 설계

## 투명성

복합체의 구성 요소인 Composite 과 Leaf 는 다른 객체지만 Comopnent 인터페이스를 적용해서 동일한 동작으로 처리합니다. 이를 투명성이라고 합니다.

다만, 투명성을 위해 서로 다른 객체에 2가지의 책임이 부여되기에 객체지향 설계의 단일 책임과 충돌합니다.

클래스를 일반화할 때 동일한 방법으로 투명한 접근을 허용하는 것이 유용한지, 불필요한 기능을 제공하지 않고 안정적인 형태를 유지하는 것이 유용한지 잘 판단합니다.

```cpp
class Component
{
private:
    std::string name;

public:
    std::string getName()
    {
        return name;
    }

    void setName(std::string InName)
    {
        name = InName;
    }
};

class Leaf: public Component
{
public:
    Leaf(std::string InName) 
    {
        this->setName(InName);
    };

    ~Leaf() {};
};

class Composite: public Component
{
public:
    std::map<std::string, Component*> children;

    Composite(std::string InName)
    {
        this->setName(InName);
    }
    ~Composite() {};

    void addNode(Component* InComp)
    {
        children[InComp->getName()] = InComp;
    }

    void removeNode(Component* InComp)
    {
        children.erase(InComp->getName());
    }

    bool isNode(Component* InComp)
    {
        return children.empty();
    }
};
```

<br/>

## 사례

복합체 패턴은 트리 구조를 만드는 디자인입니다. 대표적으로 파일 시스템을 설명하지만 메뉴구조, 쇼핑몰 카테고리, 회우너 구조 등 트리 모양을 가진 구성 요소에 많이 사용됩니다.

<br/>

### 파일 시스템

```cpp
void tree(Component* InComp)
{
    cout << InComp->getName() << endl;
    Composite* cp = dynamic_cast<Composite*>(InComp);
    if(cp == nullptr)
        return;

    for(auto it = cp->children.begin(); it != cp->children.end(); it++)
    {
        tree(it->second);
    }
}

int main(void)
{
    Composite root = Composite("root");
    Composite home = Composite("home");
    Composite hojin = Composite("hojin");
    Composite jiny = Composite("jiny");
    Composite users = Composite("users");
    Composite temp = Composite("temp");

    Leaf img1 = Leaf("img1");
    Leaf img2 = Leaf("img2");
    Leaf img3 = Leaf("img3");
    Leaf img4 = Leaf("img4");

    root.addNode(&home);
    root.addNode(&users);

    users.addNode(&hojin);
    hojin.addNode(&img1);
    hojin.addNode(&img2);
    hojin.addNode(&img3);

    users.addNode(&jiny);
    jiny.addNode(&img4);

    root.addNode(&temp);

    tree(&root);
}
```

<br/>


