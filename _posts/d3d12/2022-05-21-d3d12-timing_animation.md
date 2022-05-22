---
title: "D3D 시간 측정과 애니메이션"

categories:
  - D3D12


tags:
  - d3d12
  - timnig
  - animation

toc: true
toc_label: Timing and Animation
toc_sticky: true

sidebar_main: true
---

애니메이션이 정확히 진행되려면 시간을 측정해야 한다. 프레임 간 시간 경과(elapsed time), 즉 애니메이션의 두 프레임 사이에 흐른 시간을 측정할 수 있어야 된다. 프레임이 높은 경우 프레임 간 경과 시간이 상당히 짦으니 정밀도가 높은 타이머가 필요하다.

<br/>

# The performance time

정밀한 시간 측정을 위해 여기선 Windows 가 제공하는 performance timer (performance counter)를 사용한다. 사용을 위해선 Windows.h 를 포함시켜야 한다.

performance timer 의 시간 측정 단위는 지나간 클럭 틱(tick) 들의 개수이다. performance timer 에서 틱 수 단위의 현재 시간을 얻기 위해선 아래처럼 사용한다.

```cpp
__int64 currTime;
QueryPerformanceCounter((LARGE_INTEGER*)&currTime);
```

초 단위 시간을 얻고싶으면, 우선 QueryPerformanceFrequence 함수를 이용해 포퍼몬스 타이머의 초당 틱 수를 알아야 한다.

```cpp
__int64 countsPerSec;
QueryPerformanceFrequency((LARGE_INTEGER*)&countsPerSec);
```

이후, 주파수의 역수를 취해 틱당 초 수를 얻는다.

```cpp
mSecondsPerCount = 1.0 / (double)countsPerSec;
```

이제 틱당 초 수 mSecondPerCount 에 틱 수 valueInCounts 를 곱하면 초 단위 시간이 나온다.

```cpp
valueInSecs = valueInCounts * mSecondsPerCount;
```

근데 QueryPerformanceCounter 함수가 돌려준 개별 측정치 자체는 별 의미가 없다. 애니메이션에는 두 측정치의 차이가 중요하다. 두 QueryPerformanceCounter 호출로 얻은 값에서 뺀 결과가 경과 시간이다.

```cpp
__int64 A = 0;
QueryPerformanceCounter((LARGE_INTEGER*)&A);
/* Do work */
__int64 B = 0;
QueryPerformanceCounter((LARGE_INTEGER*)&B);
```

특정 작업에 걸린 시간은 (B-A) 개, 즉 (B-A) * mSeondPerCount 초이다.

> 다중 프로세서 컴퓨터의 경우 QueryPerformanceCounter 의 함수가 프로세서에 따라 다른 결과가 나올 수 있다. SetThreadAffinityMAsk 함수를 적절이 이용해 주 스레드가 다른 프로세서로 전환하는 일을 방지할 수 있다.

<br/>

# GameTimer 클래스

GameTimer 의 클래스에 대해서 알아본다.

```cpp
#ifndef GAMETIMER_H
#define GAMETIMER_H

class GameTimer
{
public:
	GameTimer();

	float TotalTime()const; // in seconds
	float DeltaTime()const; // in seconds

	void Reset(); // Call before message loop.
	void Start(); // Call when unpaused.
	void Stop();  // Call when paused.
	void Tick();  // Call every frame.

private:
	double mSecondsPerCount;
	double mDeltaTime;

	__int64 mBaseTime;
	__int64 mPausedTime;
	__int64 mStopTime;
	__int64 mPrevTime;
	__int64 mCurrTime;

	bool mStopped;
};

#endif // GAMETIMER_H
```

생성자에선 퍼포먼스 타이머의 주파수를 조회해 틱당 초 수를 설정하는 것이다.

```cpp
GameTimer::GameTimer()
: mSecondsPerCount(0.0), mDeltaTime(-1.0), mBaseTime(0), 
  mPausedTime(0), mPrevTime(0), mCurrTime(0), mStopped(false)
{
	__int64 countsPerSec;
	QueryPerformanceFrequency((LARGE_INTEGER*)&countsPerSec);
	mSecondsPerCount = 1.0 / (double)countsPerSec;
}
```

<br/>

## 프레임 간 경과 시간

애니메이션의 프레임을 렌더링 하기 위해선 프레임 간의 시간이 얼마나 흘렀는 지 알아야 게임의 물체들을 경과 시간에 맞춰 적절히 갱신할 수 있다.

프레임 간 경과 시간은 이렇게 구할 수 있다. $t_i$ 가 $i$ 번째 프레임을 렌덜이 할 때 측정한 퍼포먼스 타이머 값이고 $t_{i-1}$ 이 그 이전 프레임에서의 퍼포먼스 타이머 값이라고 하자. 그렇다면 $ \Delta t = t_{i} - t_{i-1} $ 이 두 프레임 사이의 경과 시간이다.

실시간 렌더링을 위해선 framerate(초당 프레임 수) 가 30은 넘어야 한다.

```cpp
void GameTimer::Tick()
{
    if( mStopped )
    {
        mDeltaTime = 0.0;
        return;
    }

    __int64 currTime;
    QueryPerformanceCounter((LARGE_INTEGER*)&currTime);
    mCurrTime = currTime;

    // Time difference between this frame and the previous.
    mDeltaTime = (mCurrTime - mPrevTime)*mSecondsPerCount;

    // Prepare for next frame.
    mPrevTime = mCurrTime;

    // Force nonnegative.  The DXSDK's CDXUTTimer mentions that if the 
    // processor goes into a power save mode or we get shuffled to another
    // processor, then mDeltaTime can be negative.
    if(mDeltaTime < 0.0)
    {
      mDeltaTime = 0.0;   
    }
}

float GameTimer::DeltaTime()const
{
	return (float)mDeltaTime;
}
```

이 틱 메소드는 아래와 같이 호출한다.

```cpp
int D3DApp::Run()
{
    MSG msg = {0};

    mTimer.Reset();

    while(msg.message != WM_QUIT)
    {
        // If there are Window messages then process them.
        if(PeekMessage( &msg, 0, 0, 0, PM_REMOVE ))
        {
          TranslateMessage( &msg );
          DispatchMessage( &msg );
        }
        // Otherwise, do animation/game stuff.
        else
        {	
            mTimer.Tick();

            if( !mAppPaused )
            {
              CalculateFrameStats();
              Update(mTimer);	
              Draw(mTimer);
            }
            else
            { 
              Sleep(100);
            }
        }
    }

    return (int)msg.wParam;
}
```

여기서 보듯 응용 프로그램은 $\Delta t$ 를 계산해서 Update 에 넘겨준다. GameTimer::Reset 의 메소드는 아래와 같다.

```cpp
void GameTimer::Reset()
{
    __int64 currTime;
    QueryPerformanceCounter((LARGE_INTEGER*)&currTime);

    mBaseTime = currTime;
    mPrevTime = currTime;
    mStopTime = 0;
    mStopped  = false;
}
```

Reset 에서 mPrevTime 을 currTime 으로 설정한다는 것을 주의하자. 애니메이션의 첫 프레임에는 이건 프레임이라는 것이 없으므로 0으로 설정해준다. 즉, 메시지 루프를 시작하기 전 Reset 을 호출해 메소드 안의 이전 시간 값을 초기화 해 준다.

<br/>

## 전체 시간

유용하게 사용할 수 있는 또 다른 시간 측정 값은, 응용 프로그램이 시작된 이후 흐른 시간이 있다. 이것을 전체 시간(total time)이라고 부른다. 예를 들어 플레이어가 어떤 레벨을 300초 안에 깨야 한다고 하면 $t_{start}$ 를 저장해놓은 뒤 주기적으로 $t$ 를 점검한다. 이후 만약 $t - t_{start} > 300s$ 이라면 레벨을 깨지 못한 것이니 플레이어의 패배가 된다.

전체 시간을 기준으로 하는 애니메이션은 정지 시간을 누적시간에 포함하면 안된다. Reset 에서 설정된 mBaseTime은 응용 프로그램 수명 전체에서 변하지 않는다. mPauseTime은 일시 정지된 시간 동안 계속해서 누적된다. 이제 유효한 전체 시간을 구하려면 total time 에서 mPauseTime 의 시간을 빼야 한다.

![tc_tt](/assets/images/d3d12/tm_tt.png)

```cpp
void GameTimer::Start()
{
    __int64 startTime;
    QueryPerformanceCounter((LARGE_INTEGER*)&startTime);


    // Accumulate the time elapsed between stop and start pairs.
    //
    //                     |<-------d------->|
    // ----*---------------*-----------------*------------> time
    //  mBaseTime       mStopTime        startTime     

    if( mStopped )
    {
      mPausedTime += (startTime - mStopTime);	

      mPrevTime = startTime;
      mStopTime = 0;
      mStopped  = false;
    }
}

void GameTimer::Stop()
{
    if( !mStopped )
    {
      __int64 currTime;
      QueryPerformanceCounter((LARGE_INTEGER*)&currTime);

      mStopTime = currTime;
      mStopped  = true;
    }
}

// Returns the total time elapsed since Reset() was called, NOT counting any
// time when the clock is stopped.
float GameTimer::TotalTime()const
{
    // If we are stopped, do not count the time that has passed since we stopped.
    // Moreover, if we previously already had a pause, the distance 
    // mStopTime - mBaseTime includes paused time, which we do not want to count.
    // To correct this, we can subtract the paused time from mStopTime:  
    //
    //                     |<--paused time-->|
    // ----*---------------*-----------------*------------*------------*------> time
    //  mBaseTime       mStopTime        startTime     mStopTime    mCurrTime

    if( mStopped )
    {
        return (float)(((mStopTime - mPausedTime)-mBaseTime)*mSecondsPerCount);
    }

    // The distance mCurrTime - mBaseTime includes paused time,
    // which we do not want to count.  To correct this, we can subtract 
    // the paused time from mCurrTime:  
    //
    //  (mCurrTime - mPausedTime) - mBaseTime 
    //
    //                     |<--paused time-->|
    // ----*---------------*-----------------*------------*------> time
    //  mBaseTime       mStopTime        startTime     mCurrTime

    else
    {
        return (float)(((mCurrTime-mPausedTime)-mBaseTime)*mSecondsPerCount);
    }
}
```

<br/>

