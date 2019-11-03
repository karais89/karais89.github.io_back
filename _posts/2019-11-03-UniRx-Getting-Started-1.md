---
layout: post
title: "UniRx 입문 1"
excerpt: "UniRx 입문 1 번역"
date: 2019-11-03 08:31:00 +0900
tags: [unity3d, unirx]
---
* TOC
{:toc}

## 환경

- macOS Catalina v10.15
- Unity 2019.2.9f1
- Github Desktop
- Rider 2019.2
- UniRx v7.1.0

원문 : [https://qiita.com/toRisouP/items/2f1643e344c741dd94f8](https://qiita.com/toRisouP/items/2f1643e344c741dd94f8)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

[UniRx 입문 시리즈 목차는 이쪽](https://qiita.com/toRisouP/items/00b8a5bb8e7b68e0686c)

---

### 서문

나는 과거에 UniRx에 대한 포스트를 산발적으로 쓰고 있었지만, 체계적으로 학습 할 수 있는 형태는 아니었습니다.

그래서 처음부터 읽어 가면서 체계적으로 UniRx를 학습 할 수 있는 자료를 만들려고 본 포스트를 쓰기로 했습니다.

(여유가 있을 때 작성되기 때문에 부정기 연재가 되지 않을까 생각합니다만 작 부탁드립니다)

## 0. 소개 "UniRx 이란?"

UniRx는 [neuecc](https://twitter.com/neuecc) 님이 작성한 **Reactive Extensions for Unity 라이브러리** 입니다.

Reactive Extensions (이하 Rx)는 요점만 말하면 다음과 같은 라이브러리 입니다.

- [MiscrosoftResearch](https://ko.wikipedia.org/wiki/%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%86%8C%ED%94%84%ED%8A%B8_%EB%A6%AC%EC%84%9C%EC%B9%98)가 개발하고 있던 C#용 비동기 처리를 위한 라이브러리
- 디자인 패턴 중 하나인 [Observer 패턴](https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4)을 기반으로 설계되어 있다.
- 시간에 관련된 처리 및 실행 타이밍이 중요한 곳에서 처리를 쉽게 작성할 수 있도록 되어 있다.
- 완성도가 높고, Java, JavaScript, Swift 등 다양한 언어로 포팅되어 있다.

UniRx는 Rx를 기반으로 Unity에 이식된 라이브러리이며, 본가 .NET Rx에 비해 다음과 같은 차이가 있습니다.

- Unity C#에 최적화되어 만들어져있다.
- Unity 개발에 유용한 기능이나 오퍼레이터가 추가적으로 구현되어 있다.
- ReactiveProperty 등이 추가되어 있다.
- 철저하게 성능 튜닝이 진행되어, 원래 .NET Rx보다 메모리 퍼포먼스가 더 좋다.

처음 이 포스트를 읽는 분들에게는 위의 설명으로는 잘 이해되지 않을 것 같기 때문에, 여기는 대략적으로 이런 특징이 있구나 넘기고, 구체적인 설명에 들어가겠습니다.

## 1. "event와 UniRx"

### event

여러분은 C# 표준 기능의 하나인 [event](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/keywords/event)를 이용한 적이 있으십니까?

event는 어떤 타이밍에서 메시지를 확인하고 다른 위치에 쓴 처리를 실행시킬 수 있는 기능입니다.

event 예 (event를 발행하는 측)

```cs
using System.Collections;
using UnityEngine;

/// <summary>
/// 100에서 카운트 다운 값을 보고하는 샘플
/// </summary>
public class TimeCounter : MonoBehaviour
{
    /// <summary>
    /// 이벤ㅌ 핸들러 (이벤트 메시지의 형식 정의)
    /// </summary>
    public delegate void TimerEventHandler(int time);

    /// <summary>
    /// 이벤트
    /// </summary>
    public event TimerEventHandler OnTimeChanged;

    private void Start()
    {
        // 타이머 시작
        StartCoroutine(TimerCoroutine());        
    }

    private IEnumerator TimerCoroutine()
    {
        // 100에서 카운트 다운
        var time = 100;
        while (time > 0)
        {
            time--;

            // 이벤트 알림
            OnTimeChanged(time);

            // 1초 기다리는
            yield return new WaitForSeconds(1);
        }
    }
}
```

event 예 (event를받는 측)
```cs
using UnityEngine;
using UnityEngine.UI;

public class TimerView : MonoBehaviour
{
    // 각 인스턴스는 인스펙터에서 설정
    [SerializeField] private TimeCounter timeCounter;
    [SerializeField] private Text counterText; // UGUI의 Text

    private void Start()
    {
        // 타이머 카운터가 변화한 이벤트를 받고 UGUI Text를 업데이트
        timeCounter.OnTimeChanged += time => // "=>" 는 람다식이라는 익명 함수 표기법 
        {
            // 현재 타이머 값을 UI에 반영
            counterText.text = time.ToString();
        };
    }
}
```

위의 코드는 타이머 카운트 다운 숫자를 이벤트로서 통지, 이벤트를 받는 측에서 UGUI Text를 갱신하는 샘플 코드 입니다.

## UniRx

왜 갑자기 event의 이야기를 했느냐하면 **"UniRx는 event의 완전한 상위 호환이며, event에 비해 보다 유연한 기술을 사용할 수 있다"** 이기 때문입니다.

앞의 코드는 UniRx를 이용하면 다음과 같이 작성할 수 있습니다.

이벤트 게시자

```cs
using System;
using System.Collections;
using UniRx;
using UnityEngine;

/// <summary>
/// 100에서 카운트 다운하고 Debug.Log에 그 값을 표시하는 샘플
/// </summary>
public class TimeCounterRx : MonoBehaviour
{
    // 이벤트를 발행하는 핵심 인스턴스
    private Subject<int> timerSubject = new Subject<int>();

    // 이벤트의 구독자만 공개
    public IObservable<int> OnTimeChanged => timerSubject;

    private void Start() => StartCoroutine(TimerCoroutine());

    private IEnumerator TimerCoroutine()
    {
        // 100에서 카운트 다운
        var time = 100;
        while (time > 0)
        {
            time--;

            // 이벤트를 발행
            timerSubject.OnNext(time);

            // 1초 기다리는
            yield return new WaitForSeconds(1);
        }
    }
}
```

이벤트 구독자

```cs
using UnityEngine;
using UnityEngine.UI;
using UniRx;

public class TimerViewRx : MonoBehaviour
{
    // 각 인스턴스는 인스펙터에서 설정
    [SerializeField] private TimeCounterRx timeCounter;
    [SerializeField] private Text counterText; // UGUI의 Text

    private void Start() =>
        // 타이머 카운터가 변화한 이벤트를 받고 UGUI Text를 업데이트
        timeCounter.OnTimeChanged.Subscribe(time =>
        {
            // 현재 타이머 값을 ui에 반영
            counterText.text = time.ToString();
        });
}
```

위의 event로 구현하고 있던 코드를 UniRx로 변환한 코드 입니다.

event 대신 Subject라는 클래스가 이벤트 핸들러를 등록할 때 Subscribe 하는 방법이 등장하고 있습니다. 즉 UniRx는

**Subject가 이벤트 구조의 핵심이 되고, Subject에 값을 전달(OnNext)하고 Subject에 구독 (Subscribe)해서 메시지를 전달 될 수 있는** 시스템으로 되어 있다는 것을 알 수 있다고 생각합니다.

그럼 다음 섹션에서 "OnNext와 Subscribe"에 대해 자세히 설명하겠습니다.

## 2. "OnNext와 Subscribe"

OnNext와 Subscribe는 모두 "Subject에 구현된 메서드"이며, 각각 다음과 같은 동작을 하고 있습니다.

- Subscribe: 메시지 수신시 실행을 **함수에 등록한다.**
- OnNext: Subscribe에 **등록 된 함수에 메시지를 전달하고 실행한다.**

아래 코드를 참조하십시오.

OnNext와 Subscrbie

```cs
private void Start()
{
    // Subject 작성
    Subject<string> subject = new Subject<string>();

    // 3회 Subscrbie
    subject.Subscribe(msg => Debug.Log("Subscribe1 : " + msg));
    subject.Subscribe(msg => Debug.Log("Subscribe2 : " + msg));
    subject.Subscribe(msg => Debug.Log("Subscribe3 : " + msg));

    // 이벤트 메시지 발행
    subject.OnNext("안녕하세요");
    subject.OnNext("안녕");
}
```

실행결과

    Subscribe1 : 안녕하세요
    Subscribe2 : 안녕하세요
    Subscribe3 : 안녕하세요
    Subscribe1 : 안녕
    Subscribe2 : 안녕
    Subscribe3 : 안녕

이와 같이, "Subscribe는 메시지 수신시의 처리를 등록 처리", "OnNext는 메시지를 발행하고 Subscribe에 등록 된 처리를 순서대로 호출 해 나가는 처리"를 하고 있음을 알 수 있습니다.

![](/images/unity3d/2019-11-03-1.png)

![](/images/unity3d/2019-11-03-2.png)

![](/images/unity3d/2019-11-03-3.png)

이번에는 구체적으로 Subscribe의 행동에 초점을 맞추고, UniRx의 기본적인 작동 원리와 개념을 설명했습니다.

다음 섹션에서는 좀 더 추상화된 이미지인 **"IObserver 인터페이스"**와 **"IObservable 인터페이스"**에 대해 설명하겠습니다.

## 4. "IObserver와 IObservable"

이전에 Subject에 "OnNext"와 "Subscribe"의 2개의 메소드가 구현되어 있다고 설명했습니다.

이것은 사실 대락적인 설명이며, 정확한 설명은 아닙니다.

더 정확히 설명을 하면 **"Subject는 IObserver 인터페이스와 IObservable인터페이스 2개를 구현하고 있다"**라고 설명 할 수 있습니다.

이 IObserver 인터페이스와 IObservable 인터페이스는 도대체 무엇인가?에 대해 자세히 설명 드리겠습니다.

## IObserver 인터페이스

IObserver 인터페이스(이후 IObserver)는 Rx에서 "이벤트 메시지를 발행 할 수 있다"라는 행동을 정의한 인터페이스 입니다. 정의는 다음과 같습니다.

```cs
using System;

namespace UniRx
{
    public interface IObserver<T>
    {
        void OnCompleted();
        void OnError(Exception error);
        void OnNext(T value);
    }
}
```

※ 소스 코드는 [여기](https://github.com/neuecc/UniRx/blob/7.1.0/Assets/Plugins/UniRx/Scripts/System/IObserver.cs) 에서 인용

IObserver는 보시는 것 처럼, "OnNext", "OnError", "OnCompleted"의 3개의 메서드 정의만 있는 굉장히 간단한 인터페이스로 되어 있습니다. 방금 전까지 이용했던 OnNext 메서드는 사실 이 IObserver에 정의된 메서드였습니다.

OnError은 "발생한 오류(Exception)을 알리는 메시지를 발행하는 메서드"이며, OnCompleted는 "메시지의 발행이 완료되었음을 알리는 메서드" 입니다.

OnError, OnCompleted의 설명은 차후에 하겠습니다. 이번에는 우선 **"OnNext", "OnError", "OnCompleted"의 3가지 메서드(메시지)가 준비되어 있다** 라는 정도만 기억 하면 될 것 같습니다.

## IObservable 인터페이스

IObservable 인페이스(이후 IObservable)는 Rx에서 "이벤트 메시지를 구독 할 수 있다"라는 행동을 정의한 인터페이스 입니다.

```cs
using System;

namespace UniRx
{
    public interface IObservable<T>
    {
        IDisposable Subscribe(IObserver<T> observer);
    }
}
```

※ 소스 코드는 [여기](https://github.com/neuecc/UniRx/blob/7.1.0/Assets/Plugins/UniRx/Scripts/System/IObservable.cs) 에서 인용

이쪽은 더 심플 합니다. **Subscribe 메서드**가 단지 1개 정의되고 있습니다.

즉, 방금 전에 호출했던 Subscribe는 따지자면 이 **IObservable에 정의된 Subscribe를 실행하고 있었다**는 것을 기억하세요.

### (보충) Subscribe의 생략 호출

자, 여기서 조금 전의 Subject를 이용했을 때의 코드를 봅시다.

```cs
subject.Subscribe(msg => Debug.Log("Subscribe1:" + msg));
```

이 코드에서 위화감을 느낀 분이 있을까요? 맞습니다. IObservable에 정의된 Subscribe는 인수에 IObserver를 받고 있는데, 이 Subscribe를 쓰는 방법에서는 인수에 무명 메서드를 받고 있습니다. 어떻게 이런 일이 가능하게 되었을까요? UniRx에서는 OnNext, OnError, OnCompleted의 3개의 메시지 중에 필요한 메시지만을 이용할 수 있는 Subscribe의 생략 호출용 메서드가 IObservable에 준비되어 있기 때문입니다. ([확장 메서드의 실제 정의는 이곳을 참조](https://github.com/neuecc/UniRx/blob/7.1.0/Assets/Plugins/UniRx/Scripts/Observer.cs#L407-L430))

즉, 앞에서의

`subject.Subscribe(msg => Debug.Log("Subscribe1:" + msg));`

는 Subscribe의 여러 생략 호출 중 하나인 "OnNext만을 이용하는 경우의 생략 호출"을 이용했다는 것에 지나지 않습니다.

실제로 UniRx를 이용할 때는 이 생략 호출을 사용하는 경우가 대부분이며, `Subscribe(IObserver<T> observer)` 호출은 거의 없습니다.

Subscribe 생략 호출 예

```cs
    // OnNext만
    subject.Subscribe(msg => Debug.Log("Subscribe1:" + msg));
    
    // OnNext & OnError
    subject.Subscribe(
        msg => Debug.Log("Subscribe1:" + msg),
        error => Debug.LogError("Error" + error));
    
    // OnNext & OnCompleted
    subject.Subscribe(
        msg => Debug.Log("Subscribe1:" + msg),
        () => Debug.Log("Completed"));
    
    // OnNext & OnError & OnCompleted
    subject.Subscribe(
        msg => Debug.Log("Subscribe1:" + msg),
        error => Debug.LogError("Error" + error),
        () => Debug.Log("Completed"));
```

## Subject의 정의를 확인

여기서 조금 전까지 사용했던 Subject 클래스를 살펴 봅시다.

```cs
    namespace UniRx
    {
        public sealed class Subject<T> : ISubject<T>, IDisposable, IOptimizedObservable<T> {/*구현부 생략*/}
    }
```

Subject는 여러 인터페이스로 구현되어 있는 것 같습니다만, 이중 하나인 `ISubject<T>`의 정의를 살펴 봅시다.

```cs
namespace UniRx
{
    public interface ISubject<TSource, TResult> : IObserver<TSource>, IObservable<TResult>
    {
    }

    public interface ISubject<T> : ISubject<T, T>, IObserver<T>, IObservable<T>
    {
    }
}
```

Subject는 IObservable 및 IObserver 두 가지를 구현하고 있는, 즉 Subject는 "값을 발행한다", "값을 구독 할 수 있다"라는 두가지 기능을 가진 클래스임을 정의에서 확인할 수 있었습니다.

정리

![](/images/unity3d/2019-11-03-4.png)
 

## 5. "오퍼레이터"

위에서  "Subscribe는 IObservable에 구현되어 있는 것을 (직접/간접) 호출하고 있다"라는 것을 설명 했습니다.

사실 이것은 즉 **"IObservable이면 무엇이든 Subscribe 할 수 있다"** 라는 뜻입니다. 즉 상대가 Subject 인지 관계 없이 IObservable만 있으면 Subscrbie 할 수 있다는 것입니다.

이 방법을 응용하면 다음과 같은 것을 할 수 있습니다.

## 고찰: 이벤트 메시지를 필터링 해 본다

예를 들어 다음과 같은 구현이 있다고 하겠습니다.

```cs
private void Start()
{
    // 문자열을 발행하는 Subject
    Subject<string> subject = new Subject<string>();

    // 문자열을 콘솔에 출력
    subject.Subscribe(x => Debug.Log($"플레이어가 {x}에 충돌했습니다."));

    // 이벤트 메시지 발급
    // 플레이어가 언급한 개체의 Tag가 발행되었다는 가정
    subject.OnNext("Enemy");
    subject.OnNext("Wall");
    subject.OnNext("Wall");
    subject.OnNext("Enemy");
    subject.OnNext("Enemy");
}
```

실행결과

    플레이어가 Enemy에 충돌했습니다.
    플레이어가 Wall에 충돌했습니다.
    플레이어가 Wall에 충돌했습니다.
    플레이어가 Enemy에 충돌했습니다.
    플레이어가 Enemy에 충돌했습니다.

그런데, 위와 같이 이벤트가 발생되는 Subject가 있을 때 "Enemey에 닿을 때만 출력하고 싶다!"라고 하겠습니다.

이 정도라면 Subscribe에 if문을 쓰는 것으로 구현할 수 있지만, 그러면 이야기 진행이 되지 않기 때문에 다른 방법을 생각해 보기로 하겠습니다.

### Subject와 Subscribe 사이에  "필터링 처리"를 끼울 수 있을까?

아까도 말했지만 값을 발행하는 처리는 IObserver에, 가입 처리는 IObservable에 각각 정의되어 있습니다.

이것은 "이 두가지가 구현된 클래스를 Subject와 Subscribe에 끼우고, 거기에 필터링 처리를 할 수 있을까?" 라는 발상이 생깁니다.

![](/images/unity3d/2019-11-03-5.png)

![](/images/unity3d/2019-11-03-6.png)

이러한 발상을 기반으로, 대부분의 Subject와 Subscribe 사이에 끼워 넣어 여러가지 메시지를 처리하는 부품을 UniRx에서는 **"오퍼레이터"**라고 부릅니다.

## 필터링 운영자 "Where"

UniRx에는 다양한 오퍼레이터가 많이 준비되어 있으며, 대부분의 경우 기존 오퍼레이터를 조합하는 것만으로도 왠만한 동작은 구현가능하게 되어 있습니다. 이번에는 그 중 하나인 메시지를 필터링 하는 "Where"를 사용하여 메시지가  Enemy의 경우에만 필터링 하도록 하겠습니다.

```cs
// 문자열을 발행하는 Subject
Subject<string> subject = new Subject<string>();

// Enemy만 필터링
subject
    .Where(x => x == "Enemy") // 필터링 오퍼레이터
    .Subscribe(x => Debug.Log($"플레이어가 {x}에 충돌했습니다."));

// 이벤트 메시지 발급
// 플레이어가 언급한 개체의 Tag가 발행되었다는 가정
subject.OnNext("Wall");
subject.OnNext("Wall");
subject.OnNext("Enemy");
subject.OnNext("Enemy");
```

실행결과

    플레이어가 Enemy에 충돌했습니다.
    플레이어가 Enemy에 충돌했습니다.

이와 같이 Where 오퍼레이터를 IObservable 및 IObserver 사이에 꽃아 주는 것만으로 메시지를 필터링 할 수 있게 되었습니다.

## 다양한 오퍼레이터

UniRx에는 Where 이외에도 많은 오퍼레이터가 준비되어 있습니다. 그 일부를 소개하겠습니다.

- 필터링 "Where"
- 메시지 변환 "Select"
- 중복을 제거하는 "Distinct"
- 일정 개수가 올때까지 대기하는 "Buffer"
- 단시간에 함께 온 경우 처음만 사용하는 "ThrottleFirst"

등을 들 수 있습니다.

위 오퍼레이터는 정말 많은 오퍼레이터 중 극히 일부에 지나지 않습니다.

[UniRx가 제공하는 모든 오퍼레이터 목록](https://qiita.com/toRisouP/items/3cf1c9be3c37e7609a2f)은 다른 포스트에 정의하고 있기 때문에 그쪽의 페이지를 참조하십시오.

## 6. "스트림"

그런데, 지금까지 "Subject를 Subscribe한다"와 "Subject에 오퍼레이터를 끼워넣고 Subscribe한다"와 같은 표현을 사용했습니다. 매번 이렇게 말하면 답답하기 때문에, 이것들을 표현하는 단어인 "스트림"을 소개 하겠습니다.

UniRx에서 "스트림"은 **"메시지가 발행된 후 Subscribe에 도달 할때까지의 일련의 처리 흐름"**을 표현하는 단어 입니다.

"오퍼레이터를 결합하여 스트림을 구축", "Subscribe하여 스트림을 실행한다", "OnCompleted를 발행하여 스트림을 정시킨다"등으로 사용 됩니다.

앞으로 이 "스트림"이라는 표현은 많이 사용하기 때문에 기억 해 둡시다.

![](/images/unity3d/2019-11-03-7.png)

## 이번 정리

- UniRx의 핵심은 Subject 클래스
- Subject를 Subscribe 하는 것이 기초
- 그러나 실제로 이용할 때는 IObserver와 IObservable 인터페이스를 의식하자.
- 이벤트의 발행, 구독이 분리되어 있는 것을 활용하여 이벤트 처리를 유연하게 할 수 있도록 한 것이 "오퍼레이터"
- 오퍼레이터를 연결하여 만든 일련의 이벤트 처리의 흐름을 "스트림"이라고 부른다.

다음에는 이번에 설명하지 않은 "OnError", "OnCompleted", "Dispose"등에 대해 설명 하겠습니다,

## 보너스: Where 오퍼레이터를 구현한다면?

본문 중에서 **"IObserver와 IObservable가 구현된 클래스를 Subject와 Subscribe에 끼우고 거기에 펄터링 처리를 작성하여 필터하고 있다."**고 설명하고 실제로 그것을 구현하는 "Where" 오퍼레이터를 소개 하고 있었습니다.

하지만, 정말로 그럴 수 있는 것인가? 실제로 비슷한 행동을 하는 필터링 오퍼레이터를 스스로 정의해 보도록 합시다.

## 필터링 오퍼레이터 구현

```cs
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

/// <summary>
/// 필터링 오퍼레이터
/// </summary>
public class MyFilter<T> : IObservable<T>
{
    /// <summary>
    /// 상류가 되는 Observable
    /// </summary>
    private IObservable<T> _source;

    /// <summary>
    /// 판정식
    /// </summary>
    private Func<T, bool> _conditionalFunc;

    public MyFilter(IObservable<T> source, Func<T, bool> conditionalFunc)
    {
        _source = source;
        _conditionalFunc = conditionalFunc;
    }
    
    public IDisposable Subscribe(IObserver<T> observer)
    {
        // Subscribe되면 MyFilterOperator 본체를 만들어 반환한다.
        return new MyFilterInternal(this, observer).Run();
    }
    
    // Observer로 MyFilterInternal이 실제로 작동하는 곳
    private class MyFilterInternal : IObserver<T>
    {
        private MyFilter<T> _parent;
        private IObserver<T> _observer;
        private object _lockObject = new object();

        public MyFilterInternal(MyFilter<T> parent, IObserver<T> observer)
        {
            _parent = parent;
            _observer = observer;
        }

        public IDisposable Run()
        {
            return _parent._source.Subscribe(this);
        }

        public void OnNext(T value)
        {
            lock (_lockObject)
            {
                if (_observer == null)
                {
                    return;
                }

                try
                {
                    // 같은 경우에만 OnNext를 통과
                    if (_parent._conditionalFunc(value))
                    {
                        _observer.OnNext(value);
                    }
                }
                catch (Exception e)
                {
                    // 도중에 에러가 발생하면 에러를 전송
                    _observer.OnError(e);
                    _observer = null;
                }
            }
        }

        public void OnError(Exception error)
        {
            lock (_lockObject)
            {
                // 오류를 전파하고 정지
                _observer.OnError(error);
                _observer = null;
            }
        }
        
        public void OnCompleted()
        {
            lock (_lockObject)
            {
                // 정지
                _observer.OnCompleted();
                _observer = null;
            }
        }
    }
}
```

Dispose() 되었을 때 제대로 멈추는 것을 고려한 예외처리 때문에 상당히 복잡해 졌지만, 가장 중요한 것은 OnNext 중 다음 부분입니다.

```cs
// 같은 경우에만 OnNext를 통과
if (_parent._conditionalFunc(value))
{
    _observer.OnNext(value);
}
```

Source로 되어 있는 Observable로 부터 OnNext가 발행되면 그 메시지 값을 평가하고 조건을 충족시키면 자신의 Subscribe에게 그것을 전달해 주는 처리로 되어 있습니다.

[역주]

UniRx를 사용하다 보면, lock나 try catch문 등을 심심치 않게 보게 된다. C#에서의 멀티 쓰레드 관련 기술이나, 예외 처리에 대한 부분을 알아두면 사용하는데 도움이 될 것 같습니다.

- [C# lock 블럭](http://www.csharpstudy.com/Threads/lock.aspx)
- [C# Interlocked](https://docs.microsoft.com/ko-kr/dotnet/api/system.threading.interlocked?view=netframework-4.8)
- [C# 예외처리](http://www.csharpstudy.com/CSharp/CSharp-exception.aspx)

## 사용성을 고려하여 확장 메서드 작성

이대로 오퍼레이터를 사용한다면 일일이 인스턴스화 해줘야 되서 사용성이 상당히 나쁘기 때문에 오퍼레이터 체인으로 Filter를 사용할수 있도록 확장 메서드를 만들었습니다.

```cs
using System;

public static class ObservableOperators
{
    public static IObservable<T> FilterOperator<T>(this IObservable<T> source, Func<T, bool> conditionalFunc) 
        => new MyFilter<T>(source, conditionalFunc);
} 
```

### 직접 만든 오퍼레이터를 사용한다.

이제 준비한 오퍼레이터를 실제로 사용해 봅시다.

```cs
private void Start()
{
    // 문자열을 발행하는 Subject 
    Subject<string> subject = new Subject<string>();

    // filter를 끼고 Subscribe 보면 
    subject
        .FilterOperator(x => x == "Enemy")
        .Subscribe(x => Debug.Log(string.Format("플레이어가 {0}에 충돌했습니다", x)));

    // 이벤트 메시지 발급 
    // 플레이어가 언급 한 개체의 Tag가 발행되어, 같은 가정 
    subject.OnNext("Wall");
    subject.OnNext("Wall");
    subject.OnNext("Enemy");
    subject.OnNext("Enemy");
}
```

실행결과

    플레이어가 Enemy에 충돌했습니다
    플레이어가 Enemy에 충돌했습니다

예상대로 메시지를 필터링하여 "Enemy"만 통과하게 되었습니다.

이와 같이 **"IObserver와 IObservable의 두 가지를 구현한 클래스를 준비하고 사이에 끼워 넣으면 오퍼레이터로서 동작하여 메시지 처리가 가능하다"**를 이해하게 되었습니다. 

[입문 2](https://qiita.com/toRisouP/items/851087b4c990d87641e6)