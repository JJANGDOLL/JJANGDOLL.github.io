---
title: "싱글턴 패턴 다시보기"

categories:
  - GPP


tags:
  - gameprogrammingpattern
  - oop
  - singleton

toc: true
toc_label: Singleton
toc_sticky: true

sidebar_main: true
---

> 오직 한 개의 클래스 인스턴스만 갖도록 보장하고, 이에 대한 전역적인 접근점을 제공합니다.(GOF의 디자인 패턴)

여기선 패턴을 싱글턴을 최대한 안쓰도록 얘기한다. GOF의 부적당한 곳에 사용된 싱글턴 패턴은 득보다 실이 많다. 여기선 싱글턴 패턴을 피할 방법을 주로 다룬다.

<br/>

# 싱글턴 패턴

## 오직 한개의 인스턴스만 갖도록 보장

인스턴스가 여러 개면 제대로 동작하지 않는 시스템은 있다.

파일 시스템 API를 래핑하는 클래스가 있다고 가정하자. 파일 작업은 완료하는데 시간이 걸려서 비동기로 작동하게 만들어야 한다. 즉, 여러 작업이 동시에 진행될 수 있으니 작업들을 서로 조율해야 한다. 한쪽에선 파일을 생성하고, 다른쪽에선 파일을 삭제하려 하려는 불상사를 피하기 위해선 서로 간섭하지 못하게 해야한다.

이것을 위해선 파일 시스템 클래스로 들어온 호출은 이전 작업 전체에 대해 접근이 가능해야 한다. 아무 데서나 인스턴스를 만들 수 있다면 다른 인스턴스에선 어떤 작업 중인지 알 수가 없다. 이것을 싱글턴으로 만들면 인스턴스를 하나만 가지도록 강제할 수 있다.

<br/>

## 전역 접근점 제공

로깅, 컨텐츠 로딩, 게임 저장 등 여러 시스템에서 파일 시스템 래퍼 클래스를 필요로 한다. 각각 시스템에 따로따로 인스턴스를 만들 수 없다면 파일 시스템에 대한 접근은 어떻게 이루어지겠는가?

여기선 싱글턴 패턴이 해결책을 제공해준다.

```cpp
class FileSystem
{
public:
    static  FileSystem& instance()
    {
        if(instance_ == NULL)
        {
            instance_ = new FileSystem();
        }
    }

private:
    FileSystem() {};
    static FileSystem* instance_;
};

또는

class FileSystem
{
public:
    static  FileSystem& instance()
    {
        static FileSystem *instance = new FileSystem();
        return *instance;
    }

private:
    FileSystem() {};
};
```

C++ 11 이후 버전은 정적 지역 변수 초기화 코드가 멀티 스레드 환경에서도 딱 한번 실행되어서 thead-safe 하다.

<br/>

# 싱글턴을 왜 사용할까?

위를 보면 정말 좋아보인다. 파일 시스템 래퍼를 인수로 주고받지 않고도 어디서나 접근이 가능해졌다. 여러 인스턴스를 생성할 수도 없어서 상태가 엉망이 될 일도 없다.

<br/>

## 한 번도 사용하지 않는다면 아예 인스턴스를 생성하지 않는다.

메모리와 CPU 사용량을 줄이는건 언제든 환영이다. 싱글턴을 사용될 때 초기화 되므로 안썻다면 초기화도 안된다.

<br/>

## 런타임시에 초기화 된다.

정적 멤버 변수는 자동 초기화 되는 문제가 있다. 즉, main 이전에 정적 변수를 초기화 하기에 프로그램이 실행된 이후에 알 수 있는 정보를 활용할 수 없다. 정적 변수 초기화 순서도 컴파일러에서 보장을 해주지 않아서 다른 정적 변수에 의존할 수도 없다.

게으른 초기화는 이런 문제를 해결해준다. 싱글턴은 최대한 늦게 초기화 되기에, 그때 쯤에는 클래스가 필요로 하는 정보가 준비되어 있다. 순환 의존만 없다면 초기화시에 다른 싱글턴을 참조해도 괜찮다.

<br/>

## 싱글턴을 상속할 수 있다.

파일 시스템 레퍼가 크로스 플랫폼을 지원해야 한다면 추상 인터페이스를 만든 뒤, 플랫폼마다 구현 클래스를 만들면 된다.

```cpp
class FileSystem
{
public:
    static FileSystem& instance()
    {
#if PLATFORM == PLAYSTATION3
        static FileSystem *instance = new PS3FileSystem();
#elif PLATFORM == WII
        static FileSystem *instance = new WiiFileSystem();
#endif
        return *instance;
    }

    virtual ~FileSystem() {}
    virtual char* readFile(char* path) = 0;
    virtual void writeFile(char* path, char* contents) = 0;

protected:
    FileSystem() {};
};

class PS3FileSystem: public FileSystem
{
public:
    virtual char* readFile(char* path)
    {
        // 소니용 IO API 사용...
    }

    virtual void writeFile(char* path, char* contents)
    {
        // 소니용 IO API 사용...
    }
};

class WiiFileSystem: public FileSystem
{
public:
    virtual char* readFile(char* path)
    {
        // 닌텐도용 IO API 사용...
    }

    virtual void writeFile(char* path, char* contents)
    {
        // 닌텐도용 IO API 사용...
    }
};
```

<br/>

# 싱글턴이 왜 문제일까?

싱글턴은 대단해보인다. 하지만 다른 설계들과 마찬가지로 비용을 지불하게 된다. 필요하지 않은 곳에 싱글턴을 적용하면 다음과 같은 문제를 부딪힌다.

<br/>

## 전역변수는 위험하다

싱글턴은 치료제보단 진정제에 가깝다. 전역 상태 때문에 생길 수 있는 문제들을 보면 싱글턴 패턴으로 해결할 수 없다는 것을 알게된다. 싱글턴은 캡슐화된 전역 상태이기 때문이다.

개발자들이 오랜 시간 얻은 지혜중 하나가 전역변수는 나쁘다인데 그 이유는 아래와 같다.

<br/>

### 전역 변수는 코드를 이해하기 어렵게 만든다

남이 만든 함수에서 버그를 찾아야 할 때 함수가 전역 상태를 건드리지 않는다면 함수 코드와 매개변수만 보면 된다.

<br/>

### 전역 변수는 커플링을 조장한다

`돌멩이가 땅에 떨어질 때 소리가 나게 하는 작업`을 한다고 하자. 물리 코드와 사운드 코드 사이에 커플링이 피하게 해야겠지만, 어서 빨리 끝내고 싶은 마음에 AudioPlayer 전역 인스턴스에 접근 가능할 수 있다면 아키텍쳐를 더럽힐 수 있다. AudioPlayer 전역 인스턴스를 아예 만들지 않았다면 피할 수 있는 문제였다. 이것은, 두 모듈이 서로 몰라야 하고 다른 방법으로 문제를 해결해야 한다는 것을 알려주는 신호이다. 인스턴스에 대한 접근을 통제함으로써 커플링을 통제할 수 있다.

<br/>

### 전역변수는 멀티 스레드 같은 동시성 프로그래밍에 적합하지 않다

멀티 스레딩을 완전히 활용하진 못하더라도, 멀티 스레딩 방식에 맞게 코드를 만들어야 한다. 전역 변수가 생기면 모든 스레드가 읽고 쓸 수 있는 메모리 영역이 생기는 셈이다. 다른 스레드가 전역 데이터에 무슨 작업을 하는지 모를 수도 있다. 이러면  데드락, 레이스 컨디션 등 정말 어려운 스레드 동기화 버그가 생긴다.

<br/>

## 싱글턴은 문제가 하나뿐일 때도 두 가지 문제를 풀려 든다

인스턴스를 한개로 만들고 싶을 뿐 전역 접근을 원치 않는다면? 또는 클래스를 전역에서 접근하게 하고 싶지만 인스턴스는 여러 개 일수 있다면? 

'한 개의 인스턴스' 와 '전역 접근' 중 보통 '전역 접근'이 싱글턴 패턴이 주로 선택되는 이유다. 로깅 클래스를 떠올려보자. 모든 함수 인수에 Log 클래스 인스턴스를 추가하면 메소드 시그니처도 번잡해지고 코드 의도도 알아보기 어렵다.

간단하게 Log 클래스를 싱글턴으로 만들면 된다. 그러면 모든 함수에서 직접 Log 인스턴스에 접근이 가능하다. 근데 의도치 않게 Log 인스턴스를 하나만 만들어야 한다.

근데 이제 각 부서마다 로깅을 하기 위해 Log 클래스를 써야한다면 문제가 생긴다. 각기 다른 부서에서 같은 인스턴스로 사용하니 로그 메시지가 뒤죽박죽이 되어버린다.

로그를 여러 파일에 나누어 쓰면 좋을 것이다. 그렇다면 부서별로 로거를 만들 수 있어야 한다. 하지만 Log 클래스는 인스턴스를 한 개만 만들 수 있다.

이처럼 어디서나 접근할 수 있다는 장점이 역으로 단점이 되어버렸다.

<br/>

## 게으른 초기화는 제어할 수 없다

가상 메모리도 사용할 수 있고 성능 요구도높지 않은 데스크탑용 어플리케잇녀에선 게으른 초기화는 괜찮은 기법이다. 게임은 다르다. 메모리 할당, 리소스 로딩, 오디오 시스템 초기화 등 해야 할 일이 많다. 오디오 시스템 초기화가 오래걸린다면 초기화 시점을 직접 제어해야 한다. 처음 소리를 재생할 때 게으른 초기화를 한다면 전투 도중에 초기화가 시작되는 바람에 게임이 버벅댈 수 있다.

마찬가지로 게임에서는 메모리 단편화를 막기 위해 힙에 메모리를 할당하는 방식을 세밀하게 제어하는게 일반적이다. 시스템 초기화에 많은 메모리가 필요하다면 힙 어디에 메모리를 저장할 지 제어할 수 있도록 적절한 초기화 시점을 찾아야 한다.

<br/>

# 대안

싱글턴을 쓰기 전에 다시 한번 생각하자.

<br/>

## 클래스가 꼭 필요한가?

게임 코드에서 본 싱글턴 클래스는 다른 객체 관리용으로만 존재하는 관리자들이 많다. MonsterManager, ParticleManager, SoundManager, ManagerManager 등 모든 클래스에 관리자 클래스가 있는게 아닐까 싶다.

관리 클래스가 필요할 때도 있지만 OOP를 제대로 이해하지 못해 만드는 경우도 있다. 다음 두 클래스를 보자.

```cpp
class Bullet
{
public:
    int getX() const { return x_; }
    int getY() const { return y_; }

    void setX(int x) { x_ = x; }
    void setY(int y) { y_ = y; }

private:
    int x_, y_;
};

class BulletManager
{
public:
    Bullet* create(int x, int y)
    {
        Bullet* bullet = new Bullet();
        bullet->setX(x);
        bullet->setY(y);

        return bullet;
    }

    bool isOnScreen(Bullet& bullet)
    {
        return bullet.getX() >= 0 &&
            bullet.getX() < SCREEN_WIDTH &&
            bullet.getY() >= 0 &&
            bullet.getY() < SCREEN_HEIGHT;
    }

    void move(Bullet& bullet)
    {
        bullet.setX(bullet.getX() + 5);
    }
};
```

복잡한 부분을 제외하면 이런 코드가 널려있다. 언뜻 보면 BulletManager 를 싱글턴으로 만들어야 겠다는 생각이 들 수 있다. 그렇다면 BulletManager 인스턴스는 몇 개 필요할까? 답은 0개이다.

이렇게 쓴다면 싱글턴 문제를 해결할 수 있다.

```cpp
class Bullet
{
public:
  Bullet(int x, int y) : x_(x), y_(y) {}

  bool isOnScreen()
  {
    return x_ >= 0 && x_ < SCREEN_WIDTH &&
           y_ >= 0 && y_ < SCREEN_HEIGHT;
  }

  void move() { x_ += 5; }

private:
  int x_, y_;
};
```

서툴게 만든 싱글턴은 다른 클래스에 기능을 더해주는 도우미 정도인 경우가 많다. 가능하다면 도우미 클래스에 있던 코드를 모두 원래 클래스에 옮기자.

<br/>

## 오직 한 개의 클래스 인스턴스만 갖도록 보장하기

싱글턴이 해결하려는 첫 번쨰 문제다. 클래스 인스턴스를 하나만 보장하는 것은 중요하다. 하지만, 어디에서나 접근하도록 하고 싶은 건 아닐 수 있다.

전역 접근 없이 클래스 인스턴스만 한개로 보장하는 방법이 몇 가지 있다.

```cpp
class FileSystem
{
public:
    FileSystem()
    {
        assert(!instantiated_);
        instantiated_ = true;
    }

    ~FileSystem() { instantiated_ = false; }

private:
    static bool instantiated_;
};

bool FileSystem::instantiated_ = false;
```

이 클래스는 어디서나 인스턴스를 생성할 수 있지만, 2개 이상의 인스턴스가 되는 순간 assert 에 걸린다. 적당한 곳에서 객체를 먼저 만든다면 아무 곳에서나 인스턴스를 만드려는 것을 방지할 수 있다. 단일 인스턴스는 보장하지만 클래스의 사용에 대해선 강제하지 않는다.

다만, 이 방식은 런타임에 인스턴스 개수를 확인한다는 것이 단점이다.

<br/>

## 인스턴스에 쉬운 접근

쉬운 접근성은 싱글턴이 선택되는 많은 이유이다. 다만, 이 방식은 원치 않는 곳에서도 쉽게 접근한다는 단점이 따른다.

변수가 최대한 적은 범위로 노출하는게 일반적으로 좋다. 변수가 노출된 범위가 좁을수록 머릿속에 담아야할 코드가 줄어든다.

<br/>

### 파라미터

인자를 넘겨주는게 가장 쉬우면서도 최선인 경우가 많다.

<br/>

### 상위 클래스로 부터 얻기

많은 게임에서 클래스의 대부분 한 단계만 상속할 정도로 상속 구조가 얕고 넓게 가져간다. 게임 내 객체가 상속받는 GameObject 라는 상위 클래스가 있다고 가정하자. 이런 게임 코드의 많은 부분이 단말에 해당하는 하위 클래스에 있다. 즉, 많은 클래스에서 GameObject 상위 클래스에 접근할 수 있다. 이 점을 잘 활용하면 아래처럼 만들 수 있다.

```cpp
class GameObject
{
protected:
  Log& getLog() { return log_; }

private:
  static Log& log_;
};

class Enemy : public GameObject
{
  void doSomething()
  {
    getLog().write("I can log!");
  }
};
```

<br/>

### 이미 전역인 객체로부터 얻기

전역 상태를 모두 제거하는건 이상적이다. Game, World 같이 게임 상태를 관리하는 전역 객체와 결국은 커플링 되어 있다.

기존 전역 객체에 빌붙으면 전역 클래스 개수를 줄일 수 있다.

```cpp
class Game
{
public:
  static Game& instance() { return instance_; }

  // Functions to set log_, et. al. ...

  Log&         getLog()         { return *log_; }
  FileSystem&  getFileSystem()  { return *fileSystem_; }
  AudioPlayer& getAudioPlayer() { return *audioPlayer_; }

private:
  static Game instance_;

  Log         *log_;
  FileSystem  *fileSystem_;
  AudioPlayer *audioPlayer_;
};
```

이제 Game 클래스 하나만 전역에서 접근하면 된다. 다른 시스템에 접근하려면 아래처럼  접근하면 된다.

```cpp
Game::instance().getAudioPlayer().play(VERY_LOUD_BANG);
```

<br/>

### 서비스 중개자로부터 얻기

Game 같이 일반적인 구체클래스가 아닌 여러 객체에 대한 전역 접근을 제공하는 용도로만 사용하는 클래스를 따로 정의하는 방법도 있다.

<br/>

