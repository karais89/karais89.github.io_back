---
layout: post
title: "UniRx 입문 5 부 - 코루틴과 함께 -"
excerpt: "UniRx 입문 5 부 - 코루틴과 함께 - 번역"
date: 2020-02-26 21:45:00 +0900
tags: [unity3d, unirx]
---
* TOC
{:toc}

## 환경

- macOS Catalina v10.15
- Unity 2019.2.10f1
- Github Desktop
- Rider 2019.2
- UniRx v7.1.0

원문 : [https://qiita.com/toRisouP/items/c4b9c5701dd6c991b481](https://qiita.com/toRisouP/items/c4b9c5701dd6c991b481)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

[UniRx 입문 시리즈 목차는 이쪽]({% post_url 2019-11-17-UniRx-Getting-Started-List %})

# 0. 이전 복습

[지난번]({% post_url 2020-02-23-Getting-Started-4 %})에는 Update()를 어떻게 Observable로 변환하여 이용할 수 있는지 설명 하였습니다.

이번에는 한 걸음 더 나아가 코루틴과 함께 UniRx를 사용하는 방법을 소개하겠습니다.

# 1. 코루틴과 UniRx

Unity는 기본적으로 "코루틴"이라는 기능이 포함되어 있습니다. 이것은 본래는 이터레이터 처리를 구현하는데 사용하는 `IEnumerator`와 `yield 키워드`를 활용하여 Unity의 **주 스레드에서** 비동기 처리 같은 것을 실현하는 기능 입니다.

(간혹 착각하고 있는 사람이 있지만, 코루틴에 실행되는 작업은 Unity의 메인 스레드에서 실행됩니다. Update()와 비슷한 처리로 실행 타이밍도 대부분 같습니다. [참고](https://docs.unity3d.com/Manual/ExecutionOrder.html)

그리고 지금까지 소개해온 UniRx는 코루틴과 병행하여 적용하면 한층 더 표현의 폭을 넓힐 수 있습니다. 왜냐하면 UniRx는 **선언적**으로 기술한 스트림은 if 분기할수 없거나, 스트림의 결과를 이용하여 그대로 **절차적** 처리에 연결하는 등의 처리가 어려웠습니다. 하지만 코루틴과 UniRx를 병행함으로써 이러한 문제를 해결 할 수 있게 됩니다. 또한 코루틴의 절차적 처리의 이점을 살리면서, UniRx의 유연한 예외 처리를 사용하는 것도 가능 합니다.

UniRx와 코루틴의 조합은 정말 편리해서 꼭 사용법을 기억하고 활용했으면 좋겠습니다.

**용어 해설**

- **선언적:** 부작용이 없는 함수를 메소드 체인으로 연결해 일련의 동작을 설명하는 방식
    - 장점: 필요한 처리를 차례로 연결해 쓰는 것만으로 구현되어 가독성이 높다.
    - 단점: 필요한 작업이 너무 복작하면 기존 함수만으로는 구현할 수 없는 경우가 있다.
- **절차적**: 상태 변수나 for와 if문을 사용하여 동작을 전부 설명하는 방식
    - 장점: 내 마음대로 사용할 수 있어, 어떤 처리도 가능하다.
    - 단점: 복잡한 기술이 증가하고 가독성이 낮아진다.

[역주]

- 절차적 프로그래밍: "루틴", "서브루틴", "메소드", "함수" 등 "프로시저"를 이용한 프로그래밍 패러다임.
- 이터레이터: 컬렉션에 대해 사용자 지정 반복을 수행, yield return 문을 사용하고 각 요소를 한 번에 하나씩 반환한다. 이터레이터는 현재 위치를 기억하고 다음 반복에서는 다음 요소를 반환한다.

# 2. 코루틴에서 IObservable로 변환

우선 코루틴에서 IObservable로 변환하는 방법을 소개 합니다.

코루틴을 스트림으로 변환하면 코루틴의 결과로 그대로 UniRx 오퍼레이터 체인에 연결하여 주는 것이 가능합니다.

또한 복잡한 행동을 하는 스트림을 생성 할 때는 코루틴에서 구현하고 스트림으로 변환하는 방법을 취하는 것이 UniRx 오퍼레이터 체인만으로 스트림을 구축하는 것보다 간단하게 처리되는 경우도 있습니다.

## Ⅰ. 코루찐 종료 시간을 스트림으로 기다린다.

사용하는 것: **Observable.FromCoroutine**

결과: `IObservable<Unit>`

첫 번째 인수: `Func<IEnumerator> coroutine` 코루틴 본체

두 번째 인수: `bool publishEveryYield = false` yield 한 시간에 OnNext를 발행 하는가?

(false는 OnCompleted 직전에 1번만 발급 default = false)

`Observable.FromCoroutine`을 이용하면 코루틴 종료 시간을 스트림으로 처리 할 수 있습니다.

코루틴 종료 타이밍의 통지를 필요로 할 때 사용할 수 있습니다.

```cs
using System.Collections;
using UniRx;
using UnityEngine;

public class ConvertFromCoroutine : MonoBehaviour
{
    private void Start() =>
        Observable.FromCoroutine(NantokaCoroutine, publishEveryYield: false)
            .Subscribe(
                _ => Debug.Log("OnNext"),
                () => Debug.Log("OnCompleted")
            ).AddTo(gameObject);

    private IEnumerator NantokaCoroutine()
    {
        Debug.Log("Coroutine started");
        
        // 어떤 처리를 하고 기다리고 있는 예
        yield return new WaitForSeconds(3);
        
        Debug.Log("Coroutine finished.");
    }
}
```

실행 결과

    Coroutine started.
    Coroutine finished.
    OnNext
    OnComplted

`Observable.FromCoroutine`는 **Subscribe 될 때마다 새롭게 코루틴을 생성하고 시작하게 된다는 것에 주의하십시오.** 코루틴 하나만 시작 스트림을 공유하고 이용하고 싶다면 [스트림의 Hot 변환]({% post_url 2019-10-13-UniRx-When-is-a-Hot-Conversion %})이 필요 합니다.

덧붙여 `Observable.FromCoroutine` 에서 시작한 코루틴은 Subscribe를 Dispose하면 자동으로 중지 됩니다.

만약 코루틴에서 자신의 스트림이 Dispose 된 것을 감지하려면 코루틴의 인수 `CancellationToken`을 전달하여 Dispose를 감지 할 수 있습니다. 이때 CancellationToken은 `Observable.FromCoroutine` 에서 얻을 수 있습니다. 
```cs
Observable.FromCoroutine(token => NantokaCoroutine(token))  // token이 CancellationToken
```

## Ⅱ. 코루찐의 yield return 결과를 추출

사용하는 것: **Observable.FromCoroutineValue<T>**

결과: `IObservable<T>`

첫 번째 인수: `Func<IEnumerator> coroutine` 코루틴 본체

두 번째 인수: `bool nullAsNextUpdate = true` null일 때 OnNext를 발행하지 않는다. default = true

`Observable.FromCoroutineValue<T>`를 이용하면 코루틴의 yield return으로 반환 된 값을 꺼내 스트림으로 사용할 수 있습니다.

`yield return`는 호출 될 때마다 1 프레임 정지하는 성질이 있기 때문에 이를 이용하여 한 프레임 씩 값을 발행 할 수 있습니다.

```cs
using System.Collections;
using System.Collections.Generic;
using UniRx;
using UnityEngine;

public class ConvertFromCoroutine2 : MonoBehaviour
{
    // 이동 좌표 목록
    [SerializeField] private List<Vector2> moveList;
    
    private void Start() =>
        Observable.FromCoroutineValue<Vector2>(MovePositionCoroutine)
            .Subscribe(x => Debug.Log(x));

    /// <summary>
    /// 목록에서 값을 1 프레임씩 꺼내는 코루틴
    /// </summary>
    /// <returns></returns>
    private IEnumerator MovePositionCoroutine()
    {
        foreach (var v in moveList)
        {
            yield return v;
        }
        
        // ↑의 foreach 문은 통째로 
        // "return moveList.GetEnumerator ();"
        // 로 고쳐 써도 된다.
    }
}
```

실행 결과

![](/images/unity3d/2020-02-26-1.png)

## Ⅲ. 코루찐 내부에서 OnNext를 직접 발행하기

사용하는 것: **Observable.FromCoroutine<T>**

결과: `IObservable<T>`

첫 번째 인수: `Func<IObserver<T>, IEnumerator> coroutine` IObserver<T>를 인수로 취하는 코루틴

`Observable.FromCoroutine<T>`은 `IObserver<T>`를 제공하는 구현도 존재 합니다. 이 IObserver<T>를 코루틴에 전달하여 **코루틴의 특정 타이밍에 OnNext를 발행 할 수 있습니다.**

이 기능을 이용하면 **내부 구현은 절차적 비동기 처리로 쓰고 외부에서는 스트림으로 취급하는 것 처럼** 코루틴과 UniRx 모두의 장점을 취할 수 있습니다.

매우 편리하고 범용적인 기능이므로 꼭 기억하세요.

또한 OnCompleted는 자동으로 발급되지 않기 때문에, 코루틴 종료 시점에서 스스로 OnCompleted를 발금해 줄 필요가 있습니다.

```cs
using System;
using System.Collections;
using UniRx;
using UnityEngine;

public class ConvertFromCoroutine3 : MonoBehaviour
{
    // 일시 정지 플래그, true인 경우 타이머 중지
    public bool IsPaused;
    
    private void Start() =>
        Observable.FromCoroutine<long>(observer => CountCoroutine(observer))
            .Subscribe(x => Debug.Log(x))
            .AddTo(gameObject);

    /// <summary>
    /// 일시 정지 플래그가 지나지 않은 상태의 시간(초)를 계산하여 알려준다.
    /// </summary>
    /// <param name="observer">알림 IObserver</param>
    /// <returns></returns>
    IEnumerator CountCoroutine(IObserver<long> observer)
    {
        long current = 0;
        float deltaTime = 0;
        
        // Dispose하면 코루틴이 멈추니까 while(true) 해도 문제없이 움직인다.
        // 기분 나쁘다면 CancellationToken을 받아 이용하면 된다.
        while (true)
        {
            if (!IsPaused)
            {
                // 일시 플래그가 지나지 않은 사이 시간을 측정한다.
                deltaTime += Time.deltaTime;
                if (deltaTime >= 1.0f)
                {
                    // 차이가 1초를 초과한 경우 정수 부분을 꺼내 집계 통지한다.
                    var integerPart = (int) Mathf.Floor(deltaTime);
                    current += integerPart;
                    deltaTime -= integerPart;
                    
                    // 시간(초) 통지
                    observer.OnNext(current);
                }
            }
            yield return null;
        }
    }
}
```

실행 결과

![](/images/unity3d/2020-02-26-2.gif)

 (일시 정지 플래그가 true 동안 카운트를 정지 해, false가 되면 중단된 이전의 카운트에서 측정을 재개)

"상태에 의존한 처리" 나 "중간에 처리가 크게 분기되는 처리" 같은 것은 UniRx 오퍼레이터 체인만으로 구현하기 어렵고, 경우에 따라서는 구현 불가능한 경우도 있습니다. 그런 경우 이렇게 코루틴에서 내부 구현을 실시하고 스트림으로 변환 해버리는 방법을 취하는 것을 권장합니다.

## Ⅳ.보다 저렴한 비용으로 가벼운 코루틴을 실행

사용하는 것: **Observable.FromMicroCoroutine** / **Observable.FromMicroCoroutine<T>**

반환 값: `IObservable<Unit>` / `IObservable<T>`

첫번째 인수: `Func<IEnumerator> coroutine` / `Func<IObserver<T>, IEnumerator> coroutine`

인수: `FrameCountType frameCountType = frameCountType.Update` Update, FixedUpdate, EndOfFrame 어느 타이밍을 이용할것인지

`Observable.FromMicroCoroutine` 그리고 `Observable.FromMicroCoroutine<T>`는 각각 이전에 설명했습니다. `Observable.FromCoroutine` / `Observable.FromCoroutine<T>`와 거의 같은 동작을 합니다.

그러나 내부 구현은 크게 다르며, **코루틴에서 yield return null 만 사용할 수 있는 제약이 있는 대신 Unity 표준 코루틴에 비해 매우 고속으로 동작하는 구조**로 되어 있습니다. 이 구조의 코루틴을 "**마이크로코루틴**"이라 부르며 UniRx의 독자 구현으로 되어 있습니다.

`yield return null`만 구현되어 있는 코루틴을 만들고 시작하려면 Unity 표준의 `StartCoroutine` 보다 이 `Observable.FromMicroCoroutine`를 사용하면 보다 더 저렴한 비용으로 코루틴을 사용할 수 있습니다.

```cs
private void Start() =>
                Observable.FromMicroCoroutine<long>(observer => CountCoroutine(observer))
            .Subscribe(x => Debug.Log(x))
            .AddTo(gameObject);
```

# 코루틴에서 IObservable로 변환하는 방법 정리

- 코루틴에서 IObservable로 변환 할 수 있다.
- `Observable.FromCoroutine` 등으로 실행한 코루틴은 `MainThreadDispatcher`에 관리가 위임되므로 수명 관리에 주의 할 필요가 있다 (AddTo 기억)
- `Observable.FromCoroutine` 등은 Subscribe 된 시점에서 새롭게 코루틴을 생성하고 시작되어 버리기 때문에, 1개의 코루틴을 공유하고 여러 번 Subscribe 할 때는 Hot 변환이 필요하다.

# 3. IObservable에서 코루틴으로 변환

두 번째 방법으로 UniRx 스트림을 코루틴으로 변환하는 방법을 소개 하겠습니다.

이 스트림을 코루틴으로 변환하는 기술을 이용하여 코루틴에서 스트림의 실행 결과를 기다리고 그대로 계속 진행하는 등의 기술 방법이 가능합니다.

"C# Task와 await에 해당한다"라고 간략하게 생각 해두면 좋을 것 같습니다.

## 스트림을 코루틴으로 변환 (Unity 5.3)

사용하는 것: ToYieldInstruction() (IObservable<T>에 대한 확장 메서드)

결과: `ObservableYieldInstruction<T>`

인수: `CancellationToken cancel` 처리를 중단한 경우는 인수에 전달 한다 (생략가능)

인수: `bool throwOnError = false OnError`가 발생했을 때 예외 내용을 throw 할 것인가?

`ToYieldInstruction` 를 이용하여 스트림을 코루틴으로 실행 한 다음 스트림을 기다리게 할 수 있습니다.

```cs
using System;
using System.Collections;
using UniRx;
using UniRx.Triggers;
using UnityEngine;

public class ConvertToCoroutine : MonoBehaviour
{
    private void Start()
    {
        StartCoroutine(WaitCoroutine());
    }

    private IEnumerator WaitCoroutine()
    {
        // Subscribe 대신 ToYieldInstruction()을 이용하여
        // 코루틴으로 스트림을 처리 할 수 있게 된다

        // 1초 기다린다
        Debug.Log("Wait for 1 second.");
        yield return Observable.Timer(TimeSpan.FromSeconds(1)).ToYieldInstruction();

        // ToYieldInstruction()은 OnCompleted가 발행되어 코루틴 종료
        // 따라서 OnCompleted가 반드시 발행되는 스트림에서만 사용할 수 있다.
        // 무한으로 이어지는 스트림의 경우 First나 FirstOrDefault를 사용하면 좋겠다.
        Debug.Log("Press any key");

        // 아무 키나 누를 때까지 기다린다
        yield return this.UpdateAsObservable()
            .FirstOrDefault(_ => Input.anyKeyDown)
            .ToYieldInstruction();

        // FirstOrDefault 조건을 충족하면 OnNext와 OnCompleted를 모두 발행한다.
        Debug.Log("Pressed");
    }
}
```

`ToYieldInstruction`은 **OnCompleted 메시지를 받으면 yield return을 종료합니다.** 따라서 OnCompleted를 발행하지 않느 끝없는 스트림을 `ToYieldInstruction` 해 버리면 영원히 끝없는 상태가 되어 버리기 때문에 주의가 필요합니다.

또한 스트림에서 발행된 OnNext 메시지를 이용하는 경우 `ToYieldInstruction` 가 반환하는 `ObservableYieldInstruction<T>`로 변수에 저장한  결과를 가져올 수 있습니다.

```cs
using System;
using System.Collections;
using UniRx;
using UniRx.Triggers;
using UnityEngine;

public class ConvertToCoroutine2 : MonoBehaviour
{
    private void Start() => StartCoroutine(DetectCoroutine());

    private IEnumerator DetectCoroutine()
    {
        Debug.Log("Coroutine start!");
        
        // 코루틴이 시작되고 나서
        // 3초 이내에 먼저 자신을 건드린 객체를 얻는다.
        var o = this.OnCollisionEnterAsObservable()
            .FirstOrDefault()
            .Select(x => x.gameObject)
            .Timeout(TimeSpan.FromSeconds(3))
            .ToYieldInstruction(throwOnError: false);
        
        // Timeout은 지정 시간 이내에 스트림이 완료되지 않는 경우
        // OnError를 발행하는 오퍼레이터
        
        // 결과를 기다린다.
        yield return o;

        if (o.HasError || !o.HasResult)
        {
            // 아무것도 치지 않았다.
            Debug.Log("hit object is nothing.");
        }
        else
        {
            // 뭔가에 맞았다.
            var hitObject = o.Result;
            Debug.Log(hitObject.name);
        }
    }
}
```

![](/images/unity3d/2020-02-26-3.gif)

![](/images/unity3d/2020-02-26-4.gif)

## 스트림을 코루틴으로 변환 (Unity 5.2 이전)

Unity 5.2 이전에는 `ToYieldInstruction`을 사용할 수 없습니다. 대신 **StartAscoroutine**를 사용하여 동일한 작업을 수행 할 수 있습니다.

```cs
IEnumerator DetectCoroutine()
{
    GameObject result = null;
    bool isTimeout = false;

        // 코루틴이 시작되고 나서 
    // 3초 이내에 먼저 자신에 닿은 오브젝트를 취득하는
    yield return this.OnCollisionEnterAsObservable()
        .FirstOrDefault()
        .Select(x => x.gameObject)
        .Timeout(TimeSpan.FromSeconds(3))
        .StartAsCoroutine(x => result = x, error => isTimeout = true);

        // StartAsCoroutine는 첫 번째 인수의 함수 결과가 전달되기 때문에 
    // 그래서 사전에 정의 된 변수에 결과를 대입하여 결과를 얻을 수 
    // 두 번째 인수는 OnError
    if (isTimeout || result == null)
    {
        Debug.Log("hit object is nothing.");
    }
    else
    {
        var hitObject = result;
        Debug.Log(hitObject.name);
    }
}
```

## IObservable에서 코루틴으로 변환하는 방법 정리

- ToYieldInstruction 또는 StartAsCoroutine를 이용하여 스트림을 코루틴으로 변환 할 수 있다.
- 응용하면 "코루틴 도중 특정 이벤트의 발행을 기다린다" 같은 처리가 가능하게 된다.

## 응용 예

### 코루틴을 직렬로 실행하고 기다린다

CoroutineA 실행 → CoroutineA의 종료를 받고 CoroutineB 시작

```cs
private void Start() =>
    Observable.FromCoroutine(CoroutineA)
        .SelectMany(CoroutineB) // SelectMany에서 합성
        .Subscribe(_ => Debug.Log("All Coroutine Finished"));

private IEnumerator CoroutineA()
{
    Debug.Log("CoroutineA start");
    yield return new WaitForSeconds(3);
    Debug.Log("CoroutineA finished");
}

private IEnumerator CoroutineB()
{
    Debug.Log("CoroutineB start");
    yield return new WaitForSeconds(3);
    Debug.Log("CoroutineB finished");
}
```

실행 결과

    CoroutineA start
    CoroutineA finished
    CoroutineB start
    CoroutineB finished
    All coroutine finished

### 여러 코루틴을 동시에 시작하고 결과를 기다린다

CoroutineA와 CoroutineB를 동시에 시작하고 모두 종료하고 정리해 처리

```cs
private void Start() =>
        Observable.WhenAll(
            Observable.FromCoroutine<string>(o => CoroutineA(o))
            , Observable.FromCoroutine<string>(o => CoroutineB(o))
        ).Subscribe(xs =>
        {
            foreach (var x in xs)
            {
                Debug.Log("result: " + x);
            }
        });

    private IEnumerator CoroutineA(IObserver<string> observer)
    {
        Debug.Log("CoroutineA start");
        yield return new WaitForSeconds(3);
        observer.OnNext("CoroutineA done!");
        observer.OnCompleted();
    }
    
    private IEnumerator CoroutineB(IObserver<string> observer)
    {
        Debug.Log("CoroutineB start");
        yield return new WaitForSeconds(1);
        observer.OnNext("CoroutineB done!");
        observer.OnCompleted();
    }
```

실행 결과

    CoroutineB start
    CoroutineA start
    result : CoroutineA done!
    result : CoroutineB done!

### 무거운 처리를 다른 스레드에 하면서 결과를 코루틴에서 얻는다.

코루틴에서 일부 처리를 다른 스레드에서 실행하고 결과가 돌아 오면 처리를 코루틴에서 재개하도록 구현.

`Observable.Start()` 을 이용한다.

```cs
private void Start() => StartCoroutine(GetEnemyDataFromServerCoroutine());

/// <summary>
/// 서버에서 적의 정보를 당겨오는 코루틴
/// </summary>
/// <returns></returns>
private IEnumerator GetEnemyDataFromServerCoroutine()
{
    // 서버에서 xml 다운로드
    var www = new WWW ("http://api.hogehoge.com/resouces/enemey.xml");
    
    yield return www;

    if (!string.IsNullOrEmpty(www.error))
    {
        Debug.Log(www.error);
    }

    var xmlText = www.text;
    
    // ParseXml 함수를 다른 스레드에서 실행
    // Observable.Start는 인수의 함수를 ThreadPool에서 실행하는 기능
    var o = Observable.Start(() => ParseXml(xmlText)).ToYieldInstruction();
    
    // 파스 종료 대기
    yield return o;

    if (o.HasError)
    {
        // 파스 실패
        Debug.LogError(o.Error);
        yield break;
    }
    
    // 파스 결과
    var result = o.Result;
    Debug.Log(result);
    
    // 이 후 처리 계속
}

private Dictionary<string, EnemyParameter> ParseXml(string xml)
{
    // 여기에 xml 파싱을 Dictinonary에 넣는다는 가정
    return new Dictionary<string, EnemyParameter>();
}

/// <summary>
/// 적 매개 변수
/// </summary>
private struct EnemyParameter
{
    public string Name { get; set; }
    public string Health { get; set; }
    public string Power { get; set; }
}
```

(↑ 구현 보다 ↓ 작성 한 것이 간단하게 작성되지만, 어디 까지나 코루틴을 사용하면 어떻게 될까 설명 이므로 용서 바랍니다.)

```cs
ObservableWWW.Get("http://api.hogehoge.com/resouces/enemey.xml") 
    .SelectMany(x => Observable.Start(() => ParseXml(x))) 
    .ObserveOnMainThread()  // 처리를 메인 스레드 취소 
    .Subscribe(result => 
    { 
        // 여기에 퍼스 결과를 사용한 처리 
    } 
    ex => Debug.LogError(ex) 
);
```

# 정리

- **스트림과 코루틴은 상호 변환 할 수 있다.**
- 코루틴을 이용하여 오퍼레이터 체인으로는 만들 수 없는 스트림을 구축하는 것이 가능하게 된다.
- UniRx의 독자 코루틴을 사용하는 것으로, Unity 표준 코루틴보다 사용이나 성능이 향상 될 수 있다.
- 스트림을 코루틴으로 변환하여 async/await 같은 기능이 가능하다 (어디 까지나 비슷한 기능)