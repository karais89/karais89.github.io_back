---
layout: post
title: "UniRx와 코루틴"
excerpt: "UniRx와 코루틴 번역"
date: 2019-10-13 22:22:00 +0900
tags: [unity3d, unirx]
---
* TOC
{:toc}

## 환경

- macOS Mojave v10.14.6
- Unity 2019.2.5f1
- Github Desktop
- Rider 2019.2
- UniRx v7.1.0

원문 : [https://qiita.com/toRisouP/items/dc369ff4232c5c127437](https://qiita.com/toRisouP/items/dc369ff4232c5c127437)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

**이 기사는 좀 오래되기 때문에 아래 새로운 문서를 참조하십시오**

[UniRx 입문 5부 - 코루틴과 함께 -](https://qiita.com/toRisouP/items/c4b9c5701dd6c991b481)

---

UniRx는 코루틴과 결합하면 좀 더 편리해집니다.

이번에는 그 방법을 소개하겠습니다.

## 코루틴을 Observable로 변환

`Observable.FromCoroutine` 을 사용함으로써 코루틴을 Observable로 변환 할 수 있습니다.

FromCoroutine 대해서는 [@neuecc](https://qiita.com/neuecc) 씨 본인이 자세히 설명하고 있으므로, 여기를 참고하는 것이 가장 좋을것 같습니다.

> [Unity의 코루틴의 분해 혹은 UniRx의 MainThreadDispatcher에 대해](http://neue.cc/2014/12/18_499.html)

FromCoroutine은 특히 **코루틴에서 반환하는 Observable을 만드는 방법**을 사용할 수 있어 범용성이 높고, 팩토리 메서드나 오퍼레이터를 조합하는 것 보다 더 간결하게 처리할 수 있습니다.

### 예) 일정 조건 일때만 카운트 다운하는 타이머를 코루틴으로 만들기

반환값이 있는 Observable을 코루틴에서 만들기
```cs
private void Start()
{
    // player 초기화 같은 로직 실행
    
    // 플레이어의 생존 시간을 30초 카운트 다운
    // 타이머의 현재 카운트 [초]가 통지 된다.
    Observable
        .FromCoroutine<int>(observer => CountDownCoroutine(observer, 30, player))
        .Subscribe(count => Debug.Log(count));
}

/// <summary>
/// 플레이어가 살아있는 동안에만 카운트 다운 타이머
/// 플레이어가 죽은 경우 카운트 중지
/// </summary>
IEnumerator CountDownCoroutine(IObserver<int> observer, int startTime, Player player)
{
    var currentTime = startTime;
    while (currentTime > 0)
    {
        if (player.IsAlive)
        {
            observer.OnNext(currentTime--);
        }
        yield return new WaitForSeconds(1.0f);
    }
    observer.OnCompleted();
}
```

## Observable을 코루틴으로 변환

Observable을 코루틴으로 변환하는 경우 `StartAsCoroutine`를 사용합니다.

```cs
private void Start()
{
    StartCoroutine(CoroutineA());
}

IEnumerator CoroutineA()
{
    // 코루틴의 실행 결과를 저장하는 변수
    var result = 0;
    // Observable.Range을 코루틴으로 변환한다.
    yield return Observable
        .Range(0, 10)
        .StartAsCoroutine(c => result = c);
    
    Debug.Log("result : " + result);
}
```

실행 결과

    result : 9

StartAsCoroutine은 다음과 같은 특징이 있습니다.

- **OnCompleted가 발행 될 때까지 yield return null을 계속 반복 한다.**
- StartAsCoroutine 인수로 전달 된 함수는 OnCompleted 발행 시에 **1번만 실행되고 마지막 OnNext 값이 전달 된다.**

### StartAsCoroutine의 용도

StartAsCoroutine을 잘 사용하면 비동기 작업이 뒤얽힌 처리를 동기화스럽게 처리 할 수 있게 됩니다.

이른바 Task의 await 처리 같은 것을 제공 할 수 있게 됩니다.

```cs
private IEnumerator HeavyTaskCoroutine()
{
    // 실행 결과
    bool result = false;

    // 비동기 처리 대기
    // Observable.Start 다른 스레드에서 작업을 수행 한다.
    yield return Observable
        .Start(() => HeavyTask())
        .StartAsCoroutine(x => result = x);

    // 실행 결과를 확인한다.
    if (result)
    {
        Debug.Log("Success");
    }
    else
    {
        Debug.Log("Failure");
    }
}

/// <summary> 
/// 실행에 시간이 걸릴 무거운 처리 
/// </ summary> 
/// <returns> 성공 여부 </ returns> 
bool HeavyTask()
{
    // 무거운 처리
    Thread.Sleep(3000);

    // 실행의 성공 여부를 반환 (의사적으로 랜덤하게 true/false를 반환) 
    var random = new System.Random();
    return random.Next() % 2 == 0;
}
```

## 정리

UniRx는 코루틴과 결합하면 더 진가를 발휘합니다.

모든 것을 Rx 스트림에서 무리하게 쓰는 것이 아니라, FromCoroutine과 StartAsCoroutine을 잘 활용하면 읽기 쉽고 사용하기 쉬운 코드를 작성할 수 있습니다.