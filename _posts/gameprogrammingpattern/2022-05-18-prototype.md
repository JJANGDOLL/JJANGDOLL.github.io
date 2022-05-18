---
title: "프로토 타입 패턴 다시보기"

categories:
  - GPP


tags:
  - gameprogrammingpattern
  - oop
  - prototype

toc: true
toc_label: Prototype
toc_sticky: true

sidebar_main: true
---

> 원형이 되는(prototype)인스턴스를 사용하여 생성할 객체의 종류를 명시하고 이렇게 만든 견본을 복사해서 새로운 객체를 생성합니다. (GOF의 디자인 패턴)

<br/>

# 프로토타입 디자인 패턴

몬스터들이 영웅을 잡아먹기 위해 떼지어 몰려다닌다. 이 몬스터들은 스포너'spawner'를 통해 게임 레벨에 등장하고, 몬스터 종류마다 스포너가 있다.

```cpp
class Monster
{
    // 용사를 괴롭히기 위한 기술들....
};

class Ghost: public Monster {};
class Demon: public Monster {};
class Sorcerer: public Monster {};

class Spawner
{
public:
    virtual ~Spawner();
    virtual Monster* spawnMonster() = 0;
};

class GhostSpawner: public Spawner
{
    virtual Monster* spawnMonster()
    {
        return new Ghost();
    }
};

class DemonSpawner: public Spawner
{
    virtual Monster* spawnMonster()
    {
        return new Demon();
    }
};

//.. 대충 뭘 할지 알겠지...
```

이 코드는 영 별로다. 이런걸 프로토 타입 패턴으로 해결할 수 있다. 핵심은 **어떤 객체가 자기와 비슷한 객체를 스폰할 수 있다**라는 부분이다. 어떤 몬스터 객체든 자기와 비슷한 객체를 만드는 원형 객체로 사용할 수 있다.

```cpp
class Monster
{
public:
    virtual ~Monster() {};
    virtual Monster* clone() = 0;
};

class Ghost: public Monster
{
private:
    int health_;
    int speed_;

public:
    Ghost(int health, int speed)
        : health_(health), speed_(speed)
    {

    }

    virtual Monster* clone()
    {
        return new Ghost(health_, speed_);
    }
};

// 뭘 할지 알겠지....

class Spawner
{
private:
    Monster* prototype_;

public:
    Spawner(Monster* prototype) 
        :prototype_(prototype) {};

    ~Spawner() ;
    Monster* spawnMonster()
    {
        return prototype_->clone();
    };
};
```

이후 유령 스포너를 만드려면 유령 인스턴스를 만든 후 전달해준다.

```cpp
Monster* ghostPrototype = new Ghost(15, 3);
Spawner* ghostSpawner = new Spawner(ghostPrototype);
ghostSpawner->spawnMonster();
```

이 패턴의 장점은 클래스의 상태 까지 같이 복제한다는 부분이다. 이 패턴은 우유하고 간단하면서도 놀랍고, 외우려 노력하지 않아도 된다.

<br/>

# 얼마나 잘 동작하는가?

몬스터마다 스포너를 따로 만들진 않아도 된다. 근데 Monster 마다 clone을 다 구현해야해서 코드 양은 그닥 차이가 없다.

clone을 하다보면 애매한 문제도 생긴다. 깊은 복사를 해야할지, 얕은 복사를 해야할지? 복제된 악마도 같은 성능을 가져야만 할지?

예제를 보면 프로토타입 패턴을 써도 코드가 크게 줄진 않고, 예제도 현실적이지 않다. 요즘 나오는 왠만한 게임 엔진에서는 몬스터마다 클래스를 따로 만들진 않는다.

클래스 상속 구조가 복잡하면 유지보수가 힘들다. 그래서 요즘은 개체 종류별로 컴포넌트나 타입 객체롬 모델링 하는 것을 선호한다.

<br/>

## 스폰 함수

앞선 스포너는 별도의 스포너 클래스가 필요했다. 하지만, 답은 여러개 있는 법 아래처럼 스폰 함수를 만들어 사용할 수도 있다.

```cpp
Monster* spawnGhost()
{
    return new Ghost(15, 3);
}

typedef Monster* (*SpawnCallback)();

class Spawner
{
private:
    SpawnCallback spawn_;

public:
    Spawner(SpawnCallback spawn) 
        :spawn_(spawn) {};

    ~Spawner() ;
    Monster* spawnMonster()
    {
        return spawn_();
    };
};
```

<br/>

## 템플릿

몬스터 클래스를 템플릿 타입 매개변수로 전달해서 받는 방법도 있다.

```cpp
class Spawner
{
public:
    virtual ~Spawner() {};
    virtual Monster* spawnMonster() = 0;
};

template<class T>
class SpawnFor: public Spawner
{
public:
    virtual Monster* spawnMonster() { return new T(); }
};

Spawner* ghostSpawner = new SpawnFor<Ghost>();
```

<br/>

