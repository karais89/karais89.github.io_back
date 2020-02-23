---
layout: post
title: "UniRx 입문 3 - 스트림 소스를 만드는 방법"
excerpt: "UniRx 입문 3 - 스트림 소스를 만드는 방법 번역"
date: 2019-11-10 09:35:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/86fea641982e6e16dac6](https://qiita.com/toRisouP/items/86fea641982e6e16dac6)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

[UniRx 입문 시리즈 목차는 이쪽](https://qiita.com/toRisouP/items/00b8a5bb8e7b68e0686c)

---

## 0. 이전 복습

지난번 OnNext, OnError, OnCompleted 및 IDisposable의 용도에 대해 설명하고 스트림의 수명 관리 방법에 대해서도 설명하였습니다.

이번은 "스트림 소스를 만드는 법"에 대해 간략히 설명해 드리고자 합니다.

## 1. 스트림의 소스 (메시지 게시자)는?

UniRx의 스트림은 다음의 3가지로 구성되어 있습니다.

1. 메시지를 발행하는 소스가 되는 것 (Subject 등)
2. 메시지 전파하는 오퍼레이터 (Where, Select 등)
3. 메시지를 구독하는 것 (Subscribe)

특히 UniRx를 사용한지 얼마 안되는 사람들은 "1"의 스트림 소스를 준비하는 방법을 잘 모르실 것이라고 생각합니다. 이번에는 이 스트림의 발단이되는 스트림 소스를 만드는 방법을 소개하고 싶습니다.

## 2. 스트림 소스가 될 수 있는 목록

스트림 소스를 준비하는 방법은 여러 가지가 있습니다. UniRx에서 제공해주는 스트림 소스를 이용해도 좋으며, 스스로 스트림 소스를 만들 수도 있습니다.

UniRx를 이용할 경우 다음과 같은 방법의 스트림 소스를 제공하고 있습니다.

- Subject 시리즈를 사용
- ReactiveProperty 시리즈를 사용
- 팩토리 메서드 시리즈를 사용
- UniRx.Triggers 시리즈를 사용
- 코루틴을 변환하여 사용
- uGUI 이벤트를 변환하여 사용
- 기타 UniRx에서 준비되어 있는 것을 사용

각각 순서대로 설명하겠습니다.

## Subject 시리즈

제 1회부터 여러번 등장하고 있는 `Subject`이지만, 이것을 이용하는 패턴이 가장 기본형입니다.

직접 스트림을 만들어 자유롭게 이벤트를 발행하고 싶다고 생각했을때 이 `Subject`를 사용하면 일단은 문제 없을 것입니다.

그리고 이 `Subject`는 몇 가지 파생이 존재하고 각각 다른 행동을 취합니다. 용도에 따라 적절한 것을 사용하는 것이 좋습니다. 이번에는 테이블로 간략히 소개하고 있지만, 각 Subject의 자세한 설명은 다음번에 설명하겠습니다.

| Subject | 기능 |
|---------|-----|
|Subject<T>	가장 간단한 것. | OnNext가 실행되면 값을 발행한다. |
|BehaviourSubject<T> | 마지막으로 발행 된 값을 캐쉬하고 나중에 Subscribe 될 때 그 캐시를 반환해 준다. 초기 값을 설정 할 수도 있다. |
|ReplaySubject<T> | 과거 모든 발행 된 값을 캐쉬하고 나중에 Subscribe 될 때 그 캐시를 모두 정리해 발행한다. |
|AsyncSubject<T> | OnNext를 즉시 발행하지 않고 내부에 캐쉬하고 OnCompleted가 실행 된 시간에 마지막 OnNext 하나만 발행한다. Future 및 Promise 같은 것 |

AynscSubject는 말 그대로 Future 나 Promise 같은 것입니다. 비동기 처리를 하고 싶을때 이용할 수 있습니다.

[역주] Future나 Promise는 다른 언어에서 존재하는 개념. C++에서 std에 Future, Promise api가 포함되어 있어 해당 기능을 이용하면 비동기 처리를 실행할 수 있다.

## ReactiveProperty 시리즈

### ReactiveProperty<T>

`ReactiveProperty<T>`는 변수에 `Subject`의 기능을 붙인 것입니다. (구현도 그런 느낌으로 되어 있습니다.)

변수를 정의하기 쉽게 되어 있고 알기 쉽기 때문에, **초심자에게 추천** 합니다.

```cs
// int형의 ReactiveProperty
var rp = new ReactiveProperty<int>(10); // 초기값 지정 가능

// 일반적으로 대입하거나 값을 읽을 수 있다.
rp.Value = 20;
var currentValue = rp.Value;

// Subscribe 할 수 있다. (Subscribe시 현재 값도 발행된다)
rp.Subscribe(x => Debug.Log(x));

// 값을 다시 설정할때 OnNext 발행 된다.
rp.Value = 30;
```

실행결과

    20
    30

또한 ReactiveProperty는 인스펙터 뷰에 표시하여 이용할 수 있습니다. 이 경우 제네릭 버전이 아닌, 각각의 형태의 전용 ReactiveProeprtyProperty를 사용해야 합니다.

또한 ReactiveProperty는 다음번에 설명할 예정인 **MV(R)P 패턴**에서 그 진가를 발휘하게 됩니다. 그때 까지 확실하게 마스터 하세요!

```cs
using UniRx;
using UnityEngine;

public class TestReactiveProperty : MonoBehaviour
{
    // int형의 ReactiveProperty (인스펙터 뷰에 나오는 버전) 
    [SerializeField]
    private IntReactiveProperty playerHealth = new IntReactiveProperty(100);
    
    private void Start() => playerHealth.Subscribe(x => Debug.Log(x));
}
```

![](/images/unity3d/2019-11-10-1.png)

또한, enum도 ReactiveProperty화 해서 인스펙터 뷰에 표시 할 수도 있지만, 이쪽은 좀 더 연구가 필요합니다. 여기에 대해서는 UniRx의 저장 neuecc씨가 블로그 쪽에서 설명하고 있기 때문에 그 쪽을 참고하시면 좋을 것입니다.

[UniRx 4.8-경량 이벤트 훅과 uGUI연계에 의한 데이터 바인딩](http://neue.cc/2015/04/13_510.html)

### ReactiveCollection

`ReactiveCollection<T>`는 ReactiveProperty와 같은 것이며, **상태의 변화를 알리는 기능이 내장된 List<T> 입니다.**

ReactiveCollection은 보통의 List처럼 쓸 수 있는 데다 상태의 변화를 Subscribe 할 수 있도록 되어 있습니다. 준비되어 있는 이벤트는 다음과 같습니다.

- 요소 추가
- 요소의 제거
- 요소 수의 변화
- 요소 재정의
- 요소의 이동
- 목록 지우기

```cs
var collection = new ReactiveCollection<string>();

collection
    .ObserveAdd()
    .Subscribe(x =>
    {
        Debug.Log($"Add [{x.Index}] = {x.Value}");
    });

collection
    .ObserveRemove()
    .Subscribe(x =>
    {
        Debug.Log($"Remove [{x.Index}] = {x.Value}");
    });

collection.Add("Apple");
collection.Add("Baseball");
collection.Add("Cherry");
collection.Remove("Apple");
```

실행결과

    Add [0] = Apple
    Add [1] = Baseball
    Add [2] = Cherry
    Remove [0] = Apple

### ReactiveDictionary<T1, T2>

ReactiveDictionary의 Dictionary 버전 입니다. ReactiveCollection과 대부분 행동이 동일하므로 생략합니다.

## 팩토리 메서드 시리즈

팩토리 메서드는 UniRx가 제공하는 스트림 소스 구축 메서드 군입니다.

`Subject`만으로는 표현할 수 없는 복잡한 스트림을 쉽게 만들 수 있는 경우가 있습니다. Unity에서 UniRx를 이용하는 경우는 팩토리 메서드를 사용할 수 있는 기회는 그리 없을지도 모르지만, 어딘가에서 도움이 될 수 있다고 생각하기 때문에 기억하는 것도 좋을 것 같습니다.

그러나 팩토리 메서드 수가 많기 때문에 이용 빈도가 높은 것만 소개 하겠습니다.

만약 모든 팩토리 메서드 방법을 알고 싶다면, ReactiveX의 Operators 항목을 참고하는 것이 좋을 것 같습니다.

[ReactiveX Creating Observables 항목](http://reactivex.io/documentation/operators.html#creating)

### Observable.Create

`Observable.Create<T>`는 자유롭게 값을 발행하는 스트림을 만들 수 있는 팩토리 메서드 입니다. 예를 들어, 일정한 절차에 의해 처리 호출 규칙을 이 팩토리 메서드 내부에 은폐시켜, 결과만을 스트림에서 추출하는 방법등이 있습니다.

Observable.Create는 인수 `Func<IObserver<T>, IDisposable>`(IObserver<T>를 받고 IDisposable를 반환하는 딜리게이트)를 인수에 취합니다. 실제로 사용법을 보여주는 것이 알기 쉬울 거라고 생각 됩니다.

```cs
Observable.Create<int>(observer =>
{
    Debug.Log("Start");

    for (int i = 0; i <= 100; i += 10)
    {
        observer.OnNext(i);
    }

    Debug.Log("Finished");
    observer.OnCompleted();

    return Disposable.Create(() =>
    {
                // 종료시 처리
        Debug.Log("Dispose");
    });
}).Subscribe(x => Debug.Log(x));
```

실행결과

    Start
    0
    10
    20
    30
    40
    50
    60
    70
    80
    90
    100
    Finished
    Disposable

### Observable.Start

Observable.Start는 주어진 블록을 다른 스레드에서 실행하여 결과를 1개만 발급하는 팩토리 메서드 입니다. 비동기로 무엇인가를 처리를 하고 결과가 나오면 통지를 원할때 사용할 수 있습니다.

```cs
// 주어진 블록 내부를 다른 스레드에서 실행
Observable.Start(() =>
{
    // google의 메인 페이지를 http를 통해 get 한다.
    var req = (HttpWebRequest) WebRequest.Create("https://google.com");
    var res = (HttpWebResponse) req.GetResponse();
    using (var reader = new StreamReader(res.GetResponseStream()))
    {
        return reader.ReadToEnd();
    }
})
.ObserveOnMainThread() // 메시지를 다른 스레드에서 Unity 메인 스레드로 전환
.Subscribe(x => Debug.Log(x));
```

하나 주의할 점이 있습니다. **Observable.Start 처리를 다른 스레드에서 실행하고 그 쓰레드에서 그대로 Subscribe 내 함수를 실행합니다.** 이것은 스레드로부터 안전하지 않은 Unity에서 문제를 일으킬 수 있으므로 주의해야 합니다.

만약 메시지를 다른 스레드에서 메인 스레드로 전환하고자 하는 경우 `ObserveOnMainThread`의 오퍼레이터를 이용합시다. 이 오퍼레이터를 끼우는 것만으로, 이 오퍼레이터 이 후 Unity 메인 스레드에서 실행되도록 변환 합니다.

### Observable.Timer/TimerFrame

`Observable.Timer`은 일정 시간 후에 메시지를 발행하는 간단한 팩토리 메서드 입니다.

실제 시간을 지정하는 경우 `Timer`를 사용하고 Unity의 프레임 수로 지정하는 경우 `TimerFrame`을 이용합시다.

`Timer`, `TimerFrame`은 인수에 따라 행동이 달라집니다. 1개 밖에 지정하지 않으면 OnShot 동작으로 종료하고 2개 지정한 경우 주기적으로 메시지를 발행하는 행동입니다. 또한 스케줄러를 지정하여 실행하는 스레드를 지정할 수 있습니다.

또한 비슷한 팩토리 메서드 인 `Observable.Interval/IntervalFrame`도 존재합니다. 이것은 `Timer/TimerFrame`의 2개의 인수를 지정하는 경우의 생략 버전 같은 것으로 생각 하시면 됩니다. `Interva/IntervalFrame`은 타이머를 시작할 때까지의 시간 (첫번째 인수)를 지정할 수 없게 되어 있습니다.

```cs
// 5초 후에 메시지 발행하고 종료
Observable.Timer(TimeSpan.FromSeconds(5))
    .Subscribe(_ => Debug.Log("5초 경과했습니다."));

// 5초 후 메시지 발행 후 1초 간격으로 계속 발행
// 스스로 정지시키지 않는 한 계속 움직인다.
Observable.Timer(TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(1))
    .Subscribe(_ => Debug.Log("주기적으로 수행되고 있습니다."))
    .AddTo(gameObject);
```

`Timer`, `TimerFrame`은 정기적으로 실행을 하고자 할때는 Dispose의 작동을 기억해 주시기 바랍니다. 멈추는 것을 잊지 않고 방치하면 메모리 누수와 NullReferenceException의 원인이 됩니다.

[역주]

해당 코드에서 정기적으로 발생하는 Timer의 경우 뒤에 AddTo 메서드를 붙여, 게임오브젝트가 제거 될때 자동으로 Dispose가 호출되게 됩니다.

## UniRx.Triggers 시리즈

UniRx.Triggers는 `using UniRx.Triggers;` 를 사용하는 스트림 소스입니다. Unity 콜백 이벤트를 UniRx의 IObservable로 변환하여 제공 해주고 있습니다. **UniRx에서는 이것이 가장 중요하고 유용하다고 생각합니다.**

Triggers는 수가 매우 많기 때문에 다 소개 할수 없으므로, GitHub의 wiki를 참고하십시오.

[GitHub - UniRx.Triggers](https://github.com/neuecc/UniRx/wiki/UniRx.Triggers)

Unity가 제공하는 대부분의 콜백 이벤트를 스트림으로써 취득 가능하게 되어 있으며 **GameObject가 Destroy 될때 자동으로 OnCompleted를 발급** 해주는 구조로 되어 있기 때문에, 수명 관리도 걱정 없습니다.

```cs
using UniRx;
using UnityEngine;
using UniRx.Triggers; // 필수 추가

// <summary>
// WarpZone (라는 이름의 IsTrigger인 Collider가 붙은 영역)에
// 들어왔을때 부유하는 스크림트 (임의)
// </summary>
public class TriggersSample : MonoBehaviour
{
    private void Start()
    {
        bool isForceEnabled = true;
        var rb = GetComponent<Rigidbody>();

                // 플래그가 유요한 동안 위쪽에 힘을 가한다.
        this.FixedUpdateAsObservable()
            .Where(_ => isForceEnabled)
            .Subscribe(_ => rb.AddForce(Vector3.up * 20));

                // WarpZone에 침입하면 플래그를 활성화 한다.
        this.OnTriggerEnterAsObservable()
            .Where(x => x.gameObject.CompareTag("WarpZone"))
            .Subscribe(_ => isForceEnabled = true);
        
                // WarpZone에 나오면 플래그를 해제 한다.
        this.OnTriggerExitAsObservable()
            .Where(x => x.gameObject.CompareTag("WarpZone"))
            .Subscribe(_ => isForceEnabled = false);
    }
}
```

Triggers를 사용하여 Unity 콜백을 스트림으로 변환하면 모든 것을 Awake/Start에 작성할 수 있습니다. 이것의 이점은 다음 번에 자세히 설명하겠습니다.

## 코루틴에서 변환

사실 Unity의 코루틴과 UniRx는 아주 궁합이 잘 맞습니다. IObservable 및 코루틴은 서로 변환하여 이용하는 것이 가능 합니다.

코루틴에서 IObservable의 변환은 `Observable.FromCoroutine`을 이용하여 수행 할 수 있습니다. 오퍼레이터 체인으로 복잡한 스트림을 구축하는 것보다 코루틴을 사용해 절차적으로 쓰는 경우가 더 심플하고 알기 쉬운 경우도 존재 합니다. 코루틴은 악으로 단정짓지 말고 차라리 코루틴과 UniRx를 함께 사용하는 것이 편리하다는 것을 기억하세요.

UniRx와 코루틴의 결합에 대한 설명은 다음번에 자세히 설명하겠습니다. 이번에는 간단한 예제만 소개하는 것으로 마치겠습니다.

```cs
using System;
using System.Collections;
using UniRx;
using UnityEngine;

public class Example23_Timer : MonoBehaviour
{
    // <summary>
    // 일시 정지 플래그
    // </summary>
    public bool IsPaused { get; private set; }
    
    private void Start()
    {
        // 60초 카운트하는 스트림을 코루틴에서 만든다.
        Observable.FromCoroutine<int>(observer => TimerCoroutine(observer, 60))
            .Subscribe(t => Debug.Log(ㅅ));
    }

    // <summary>
    // 초기 값에서 0까지 카운트하는 코루틴
    // 그러나 IsPaused 플래그가 유요한 경우는 카운트 중지
    // </summary>
    IEnumerator TimerCoroutine(IObserver<int> observer, int initializeTime)
    {
        var current = initializeTime;
        while (current > 0)
        {
            if (!IsPaused)
            {
                observer.OnNext(current--);
            }
            yield return new WaitForSeconds(1);
        }
                observer.OnNext(0);
        observer.OnCompleted();
    }
}
```

## uGUI 이벤트에서 변환

UniRx는 uGUI와도 궁합이 좋고, ReactiveProperty와 결합하여 View와 Model의 관계를 굉장히 명확하게 구현할 수 있습니다. (MV(R)P 패턴이라고 불립니다.)

이번에는 MV(R)P 패턴은 설명 하지 않고 uGUI 이벤트에서 변환하는 방법만 소개 하겠습니다.

라고 해도 크게 소개할 것은 없고, UniRx를 using하고 있으면 uGUI 컴포넌트의 uGUI 이벤트로서 그대로 사용할 수 있도록 되어 있습니다.

```cs
using UniRx;
using UnityEngine;
using UnityEngine.UI;

public class Example23_uGUI : MonoBehaviour
{
    // 인스펙터에서 설정
    [SerializeField] private Button button; 
    [SerializeField] private InputField inputField;
    [SerializeField] private Slider slider;
    
    private void Start()
    {
        // uGUI의 기본 Unity 이벤트의 이름을 한 Observable이 준비되어 있다.
        button.OnClickAsObservable().Subscribe(_ =>
        {
            Debug.Log("button OnClick!");
        });

        inputField.OnValueChangedAsObservable().Subscribe(str =>
        {
            Debug.Log("inputField OnValueChanged : " + str);
        });
        inputField.OnEndEditAsObservable().Subscribe(str =>
        {
            Debug.Log("inputField OnEndEdit : " + str);
        });

        slider.OnValueChangedAsObservable().Subscribe(val =>
        {
            Debug.Log("slider value changed : " + val);
        });
        
        // ----------
        
        // 또한 이러한 방법도 있다.
        inputField.onValueChanged.AsObservable().Subscribe();
        
        // 이 두 기법의 차이는 Subscribe시 현재 값의 초기 값의 발행 여부이다
        // Subscribe시 초기 값이 필요한 경우는 전자를 사용하면 된다.
        inputField.OnValueChangedAsObservable(); // 초기값이 있다.
        inputField.onValueChanged.AsObservable(); // 초기값이 없다.
    }
}
```

## 기타

UniRx는 이외에도 편리한 스트림 소스를 제공 해주고 있습니다. 그 중 일부를 소개하겠습니다.

### ObservableWWW

`ObservableWWW`는 Unity의 `WWW`를 스트림으로 처리 할 수 있도록 래핑 해 준 것입니다. 호출하는 것으로 UniRx는 코루틴을 실행해 WWW를 처리하고 결과만 알려줍니다.

```cs
ObservableWWW.Get("https://google.com")
            .Subscribe(x => Debug.Log(x));
```

(UniRx는 내부에 코루틴을 가지고 있습니다. 그 실체가 되는 GameObject는 MainThreadDispacher 라는 이름으로 씬에 존재합니다. 이 MainThreadDispatcher를 멈추면 UniRx가 올바르게 동작하지 않게 되므로 이 GameObject를 손보는 것은 피하는 것이 현명합니다.)

[역주]

Unity 2018.3 이상 버전부터는 ObservableWWW의 사용을 하지 않는 것을 권장하고 있습니다. 그 대신에 유니티에 새로 추가된 [UnityWebReques](https://docs.unity3d.com/ScriptReference/Networking.UnityWebRequest.html)t를 사용하라고 권장하고 있습니다. 실제 편안한 사용을 위해서는 [UniTask](https://github.com/Cysharp/UniTask) 라이브러리와 같이 사용해 C#의 Task 처럼 사용을 하거나 Task를 Observable로 변환하여 사용하면 될 것으로 보인다. 자세한 내용은 [UniRx.Async](https://qiita.com/toRisouP/items/4445b6b9bf00e49eb147)를 참고

### Observable.NextFrame

이름 그대로 다음 프레임으로 메시지를 발행 해주는 스트림을 만들 수 있습니다. **메시지의 발행 타이밍은 Update가 타이밍이 아닌 코루틴 타이밍 이므로**, 실행 타이밍이 매우 중요한 경우에는 주의가 필요합니다.

참고 : [Unity 문서 이벤트 함수의 실행 순서](https://docs.unity3d.com/kr/current/Manual/ExecutionOrder.html)

```cs
Observable.NextFrame()
        .Subscribe(_ => Debug.Log("다음 프레임에서 실행됩니다."));
```

[역주] 업데이트 타이밍은 다음 3가지로 조절 가능하며, 기본 값은 Update 이다.

- Update (yield return null)
- FixedUpdate (yield return new WaitForFixedUpdate())
- EndOfFrame (yield return new WaitForEndOfFrame())

### Observable.EveryUpdate

`Observable.EveryUpdate`는 매 Update 타이밍을 알려주는 스트림 소스 입니다. UniRx.Triggers의 UpdateAsObservable 과 비슷하지만 이쪽은 GameObject에 붙어 Destroy시 OnCompleted가 실행되는 반면 `Observable.EveryUpdate`는 **스스로 중지하지 않는 한 씬을 거쳐도 계속 움직이는 스트림** 입니다. FPS 카운터와 같은 어떤 씬에서도 계속 같은 스트림을 구축해야 될때 사용하면 좋습니다.

참고: [UniRx에서 FPS 카운터를 만들어 보자.]({% post_url 2019-10-23-UniRx-FPS-Counter %})

### ObservableEveryValueChanged

ObservableEveryValueChanged는 스트림 소스 중에서도 이색적인 존재이며, class 그 자체(?)의 확장 메서드로 정의되어 있습니다. 기능으로는 **모든 객체의 파라미터를 매 프레임 모니터링하고 변화가 있었을 때에 통지하는** 스트림을 생성 할 수 있습니다.

```cs
var characterController = GetComponent<CharacterController>();
        
// CharacterController의 isGrounded를 감시
// false -> true가 되면 로그 출력
characterController
    .ObserveEveryValueChanged(c => c.isGrounded)
    .Where(x => x)
    .Subscribe(_ => Debug.Log("착지!"))
    .AddTo(gameObject);

// ↑ 코드는 ↓와 거의 동의어
Observable.EveryUpdate()
    .Select(_ => characterController.isGrounded)
    .DistinctUntilChanged()
    .Where(x => x)
    .Subscribe(_ => Debug.Log("착지!"))
    .AddTo(gameObject);

// ObserveEveryValueChanged는
// EveryUpdate + Select + DistinctUntilChanged
// 의 축약 버전에 속한다.
```

## 3. 정리

**스트림 근원(소스)를 만드는 방법은 여러가지가 있다.**

- Subject 시리즈를 사용
- ReactiveProperty 시리즈를 사용
- 팩토리 메서드 시리즈를 사용
- UniRx.Triggers 시리즈를 사용
- 코루틴을 변환하여 사용
- uGUI 이벤트를 변환하여 사용
- 기타 UniRx에서 준비되어 있는 것을 사용

"UniRx.Triggers", "ReactiveProperty", "uGUI에서 변환"이 개인적으로 최우선으로 기억해야 될 것이라고 생각합니다. 이 3가지만 기억한다면 우선 UniRx를 사용한 개발에 80% 정도는 어떻게든 된다고 생각합니다.