---
title: "경량 패턴 다시보기"

categories:
  - GPP


tags:
  - gameprogrammingpattern
  - oop
  - flyweight

toc: true
toc_label: Flyweight
toc_sticky: true

sidebar_main: true
---

> 공유를 통해 많은 수의 소립 객체들을 효과적으로 지원합니다. (GOF의 디자인 패턴)

자욱한 안개가 걷히고 웅장하고 오래된 숲이 모습을드러낸다. 거대한 나무 줄기 사이로 숲은 끝없이 이어진다.

판타지 게임에서 흔히 볼 수 있는 설정이다. 일반적으로 이런 장면은 경량 패턴으로 구현한다.

<br/>

# 숲에 들어갈 나무들

나무들이 홤녀을 꽉 채운 숲을 볼 때 그래픽스 프로그래머는 1초에 60번씩 GPU에 전달해야하는 수백만개의 폴리곤을 본다.

수천 그루가 넘는 나무마다 각각 수천 폴리곤의 형태로 표현해야 한다. 메모리가 충분하다 해도, 이런 데이터 전체를 CPU에서 GPU로 전달해야한다.

나무마다 필요한 데이터는 아래와 같다.

* 줄기, 가지, 잎의 형태를 나타내는 폴리곤 메시
* 나무 껍질과 입사귀 텍스쳐
* 숲에서의 위치와 방향
* 각각의 나무가 다르게 보이도록 크기와 음영 같은 값을 조절할 수 있는 매개변수

간단하게 짜본 클래스는 아래와 같다.

```cpp
class Tree
{
private:
    Mesh mesh_;
    Texture bark_;
    Texture leaves_;
    Vector location_;
    double height_;
    double thickness_;
    Color barkTint_;
    Color leafTint_;
}
```

데이터가 너무 많고, 메시와 텍스쳐는 크기도 크다. 이렇게 많은 객체로 이루어진 숲 전체는 1 프레임에 GPU에 보내야할 양이 너무많다. 다행히도 검증된 해결책이 있다.

핵심은 숲에 나무가 수천 그루가 있어도 대부분 비슷해 보이다는 점이다. 그렇다면 모든 나무를 같은 메시와 텍스쳐로 표시할 수 있을 듯 하다. 즉, 나무 객체에 들어있는 대부분이 인스턴스별로 별 다르지 않다.

객체를 조개서 이런 점을 명시적으로 모델링 할 수 잇다. 모든 나무가 다 같이 사용하는 데이터를 뽑아내 새로운 클래스에 모은다.

```cpp
class TreeModel
{
private:
    Mesh mesh_;
    Texture bark_;
    Texture leaves_;
};

class Tree
{
private:
    TreeModel* model_;

    Vector location_;
    double height_;
    double thickness_;
    Color barkTint_;
    Color leafTint_;
}
```

게임 내 같은 메시와 텍스쳐를 여러 번 메모리에 올릴 필요가 없기에 TreeModel 은 하나만 존재하면 된다. Tree 인스턴스는 TreeModel 을 참조하기만 한다.

이 예제에서는 TreeModel 클래스로 깔끔하게 분리할 수 있어서 기초적인 자원 공유 기법이지 패턴이라고 부를 정도는 아닌 것 처럼 보인다. 하지만 공유 객체가 명확하지 않은 경우 잘 드러나 보이지 않는다. 그런 경우에는 하나의 객체가 신기하게도 여러 곳에 동시에 존재하는 것 처럼 보인다.

<br/>

# 지형 정보

나무를 심을 땅도 게임에 표현해야 한다. 보통 풀, 흙, 언덕, 호수, 강 같은 다양한 지형을 이어 붙여 땅을 만든다. 여기서는 땅을 타일 기반으로 만든다. 모든 타일은 지형 종류 중 하나로 덮여있다.

지형 종류에는 게임플레이에 영향을 주는 여러 속성이 있다.

* 플레이어가 얼마나 빠르게 이동할 수 있는지를 결정하는 이동 비용 값
* 강이나 바다처럼 보트로 건널 수 있는 곳인지 여부
* 렌더링할 때 사용할 텍스쳐

```cpp
enum Terrain
{
    TERRAIN_GRASS,
    TERRAIN_HILL,
    TERRAIN_RIVER
};

class World
{
private:
    Terrain tiles_[WIDTH][HEIGHT];

public:
    int getMovement(int x, int y)
    {
        switch(tiles_[x][y])
        {
            case TERRAIN_GRASS:
                return 1;
            case TERRAIN_HILL:
                return 2;
            case TERRAIN_RIVER:
                return 3;
        }
    }

    bool isWater(int x, int y)
    {
        switch(tiles_[x][y])
        {
            case TERRAIN_GRASS:
                return false;
            case TERRAIN_HILL:
                return false;
            case TERRAIN_RIVER:
                return true;
        }
    }
};
```

이 코드는 동작은 하는데 너무 지저분하다. 이동 비용이나 물인지 땅인지 여부는 지형에 관한 데이터인데 이 코드에서는 하드코딩 되어있다. 게다가 같은 지형 데이터가 여러 메소드에 나뉘어져있다. 이런 데이터는 하나로 합쳐서 캡슐화 하는 것이 좋다.

```cpp
class Terrain
{
private:
    int movementCost_;
    bool isWater_;
    Texture texture_;

public:
    Terrain(int movementCost, bool isWater, Texture texture)
        : movementCost_(movementCost), isWater_(isWater), texture_(texture)
    {

    }

    int getMovementCost() const { return movementCost_; }
    bool isWater() const { return isWater_; }
    const Texture& getTexture() const { return texture_; }
};
```

하지만 매 타일바다 Terrain 인스턴스를 하나씩 만드는 비용은 피하고 싶다. Terrain 클래스는 타일 위치에 관련된 내용은 전혀 없다. 경량 패턴식으로 얘기하면 모든 지형 상태는 `고유`하다.

따라서 지형 종류별로 Terrain 객체를 만들 필요는 없다.

Terrain 인스턴스가 여러 곳에서 사용되니 동적 할당하면 생명주기를 관리하기가 어렵다. 따라서 World에 관리한다. 또한, 지형을 아래처럼 생성할 수 있다.

```cpp
class World
{
private:
    Terrain grassTerrain_;
    Terrain hillTerrain_;
    Terrain riverTerrain_;

    Terrain* tiles_[WIDTH][HEIGHT];

public:
    World()
        : grassTerrain_(1, false, GRASS_TEXTURE),
        hillTerrain_(3, false, HILL_TEXTURE),
        riverTerrain_(2, true, RIVER_TEXTURE)
    {

    }

    void generateTerrain()
    {
        for(int x = 0; x < WIDTH; x++)
        {
            for(int y = 0; y < HEIGHT; y++)
            {
                if(random(10) == 0)
                {
                    tiles_[x][y] = &hillTerrain_;
                }
                else
                {
                    tiles_[x][y] = &grassTerrain_;
                }
            }
        }

        int x = random(WIDTH);
        for(int y = 0; y < HEIGHT; y++)
        {
            tiles_[x][y] = &riverTerrain_;
        }
    }

    const Terrain& getTile(int x, int y)
    {
        return *tiles_[x][y];
    }
}
```

World 클래스는 더이상 지형의 세부 정보와 커플링 되지 않는다. 타일 속성은 Terrain 객체에서 바로 얻어올 수 있다.

```cpp
int cost = world->getTile(2, 3).getMovementCost();
```

<br/>

# 성능에 관하여

깐깐한 사람이라면 포인터가 열거형보다 느리다고 트집잡을만한 요소가 있다. 지형 데이터를 포인터로 접근한다는 것은 간접 조회 한다는 뜻이다.  지형 데이터 조회를 위해선 격자 데이터부터 객체 지형 포인터를 얻은 다음, 포인터를 통해 이동 비용 값을 얻어야 한다. 이렇게 포인터를 따라가면 캐시 미스가 발생할 수 있어 성능이 조금 떨어질 수 있다.

최적화의 황금률은 언제나 **먼저 측정** 하는 것이다. 최신 컴퓨터 하드웨어는 너무 복잡해서 추측 만으로는 최적화 하기 어렵다. 측정해본 결과 경량 패턴을 써도 열거형을 쓴 것 보다 성능이 나빠지진 않았다. 오히려 경량 패턴 방식이 더 빨랐다. 하지만, 이건 메모리에 어떤 식으로 배치되느 냐에 따라 달라질 수 있다.

확실한 것은 경량 객체를 한번은 고려해봐야 한다는 것이다. 열거형을 선언해 수많은 다중 선언문을 만들 생각이라면 경량 패턴을 고려해보자.

<br/>

