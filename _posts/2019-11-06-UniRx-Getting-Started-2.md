---
layout: post
title: "UniRx 입문 2 - 메시지의 종류/스트림의 수명"
excerpt: "UniRx 입문 2 - 메시지의 종류/스트림의 수명 번역"
date: 2019-11-06 22:00:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/851087b4c990d87641e6](https://qiita.com/toRisouP/items/851087b4c990d87641e6)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

[UniRx 입문 시리즈 목차는 이쪽](https://qiita.com/toRisouP/items/00b8a5bb8e7b68e0686c)

---

## 0. 이전 복습

지난번 IObserver 인터페이스는 다음과 같이 정의된다고 설명했습니다.
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

지난번에는 설명의 편의상 OnNext만 설명 했습니다.

이번에는 생략했던 "OnError", "OnCompleted" 및 "Dispose"에 대해 설명하겠습니다.

## 1. "OnNext", "OnError", "OnCompleted"

UniRx에서 발행되는 메시지는 모두 이 3가지 중 어느 하나가 되며, 다음과 같은 용도로 이용되고 있습니다.

- **OnNext**: 통상 이벤트가 발행되었을 때 통지되는 메시지
- **OnError**: 스트림 처리 중 예외가 발생했음을 통지한다.
- **OnCompleted**: 스트림이 종료되었음을 통지한다.

## "OnNext"메시지

OnNext는 UniRx에서 가장 많이 사용되는 메시지이며, 보통 "이벤트 통지" (EventArgs 포함)을 나타냅니다.

가장 많이 이용되는 메시지이며, 사용법에 따라서는 이 OnNext 메시지만을 기억하고 있어도 문제가 없는 경우도 많습니다.

### 예 1. 정수값 통지

```cs
var subject = new Subject<int>();

subject.Subscribe(x => Debug.Log(x));
subject.OnNext(1);
subject.OnNext(2);
subject.OnNext(3);
subject.OnCompleted();
```

실행결과

    1
    2
    3

예 1은 간단하게 정수를 통지하고 구독 측에서는 Debug.Log에 표시하는 단순한 샘플 코드 입니다.

### 예 2. "의미"없는 값을 통지

```cs
var subject = new Subject<Unit>();

subject.Subscribe(x => Debug.Log(x));

// Unit 형은 그 자체는 별 의미가없다.
// 메시지의 내용에 의미가 아니라 이벤트 알림 타이밍이 중요한 순간에 사용할 수 있다.
subject.OnNext(Unit.Default);
```

실행결과

    ()

예 2는 **Unit형** 이라는 특수한 형태를 발행하고 있습니다.

이 형태는 "메시지의 내용물에 의미는 없다"라는 표현을 할 때 사용합니다.

이것은 "이벤트가 발생된 타이밍이 중요하며, OnNext 메시지 내용은 상관 없다"라는 경우에 사용할 수 있습니다.

예를 들어, "씬의 초기화 완료", "플레이어 사망"등에서 사용할 수 있습니다.

### 예 3. 씬의 초기화 완료 후 Unit형 통지

```cs
public class GameInitializer : MonoBehaviour
{
    // Unit형 사용
    private Subject<Unit> initializedSubject = new Subject<Unit>();

    public IObservable<Unit> OnInitializedAsync => initializedSubject;

    private void Start()
    {
        // 초기화 시작
        StartCoroutine(GameInitializeCoroutine());

        OnInitializedAsync.Subscribe(_ => { Debug.Log("초기화 완료"); });
    }

    private IEnumerator GameInitializeCoroutine()
    {
        /*
            * 초기화 처리
            *
            * WWW 통신이나 개체 인스턴스화 등
            * 시간이 걸리고 무거운 처리를 여기에서 한다고 가정
            */
        yield return null;
        
        // 초기화 완료 통지
        initializedSubject.OnNext(Unit.Default); // 타이밍이 중요한 통지이므로 Unit로도 충분하다.
        initializedSubject.OnCompleted();
    }
}
```

코루틴으로 게임의 초기화를 수행하고 처리가 완료되면 이벤트를 발행해 통지하는 클래스 구현 예 입니다.

이러한 이벤트는 이벤트의 내용물 값은 무엇이든 상관없는 상황에서 Unit형을 사용하는 경우가 많습니다.

(또한 예3에서는 Subject를 사용했지만 이 경우는 AsyncSubject가 적합 할지도 모릅니다. AsyncSubject에 대해서는 다음에 설명하겠습니다.)

## "OnError" 메시지

OnError 메시지는 이름 그대로 예외가 스트림 도중에 발생했을 때에 통지되는 메시지로 되어 있습니다.

OnError 메시지 스트림 도중에 catch하여 처리하거나 그대로 Subscribe 메서드에 도달시켜 처리 할 수 있습니다. **만약 OnError 메시지가 Subscribe까지 도달 한 경우, 그 스트림 구독은 종료되고 파기 되어 버립니다.**

### 예 4. 도중에 발생한 예외를 Subscribe로 받는다

```cs
var stringSubject = new Subject<string>();
        
// 문자열을 스트림 중간에서 정수로 변환 
stringSubject
    .Select(str => int.Parse(str)) // 숫자를 표현하는 문자열이 아닌 경우는 예외가 나온다 
    .Subscribe(
        x => Debug.Log("성공:" + x), // OnNext생
        ex => Debug.Log("예외가 발:" + ex) //생 OnError
    );

stringSubject.OnNext("1");
stringSubject.OnNext("2");
stringSubject.OnNext("Hello"); // 이 메시지에서 예외가 발
stringSubject.OnNext("4");
stringSubject.OnCompleted();
```

실행결과

    성공 : 1
    성공 : 2
    예외가 발생 : System.FormatException : Input string was not in the correct format

※ Subscribe의 오버로드 중 Subscribe(OnNext, OnError)를 받는 메서드를 이용하고 있습니다

예 4는 OnNext로 보내져 온 문자열을 Select 오퍼레이터(값의 변환)에서 int로 캐스팅해서 표시하는 스트림을 사용한 예입니다.

이와 같이 스트림 중간에 예외가 발생했다면 OnError 메시지가 발행되고 Subscribe에게 통지가 오고 있는 것을 알 수 있습니다.

또한 OnError을 받은 후 `OnNext("4")`는 처리가 되지 않습니다. 이처럼 **"OnError를 Subscribe가 감지하면 스트림 구독을 중단 한다"**는 것을 기억하십시오. 

### 예 5. 도중에 예외가 발생하면 다시 구독하기

```cs
var stringSubject = new Subject<string>();

// 문자열을 스트림 중간에서 정수로 변환 
stringSubject
    .Select(str => int.Parse(str))
    .OnErrorRetry((FormatException ex) => // 예외의 형식으로 필터링 가능
    {
        Debug.Log("예외가 발생하여 다시 구독 합니다");
    })
    .Subscribe(
        x => Debug.Log("성공:" + x), // OnNext
        ex => Debug.Log("예외가 발생:" + ex) // OnError
    );

stringSubject.OnNext("1");
stringSubject.OnNext("2");
stringSubject.OnNext("Hello");
stringSubject.OnNext("4");
stringSubject.OnNext("5");
stringSubject.OnCompleted();
```

실행결과

    성공 : 1
    성공 : 2
    예외가 발생하여 다시 구독합니다
    성공 : 4
    성공 : 5

예 5는 도중에 예외가 발생했을 경우 `OnErrorRetr`로 스트림을 재 구축하고 구독을 계속하고 있습니다.

OnErrorRetry는 OnError가 특정 예외인 경우에 다시 Subscribe를 시도해주는 예외 핸들링 오퍼레이터 입니다. (여기서 말하는 Subscribe를 다시 시작한다는 것은 Subject에 IObserver을 다시 등록하러 간다는 뜻입니다)

|하고 싶은 일 | 오퍼레이터 | 비고 |
|------------|-----------|------|
|OnError가 오면 다시 Subscribe 하고 싶다.| Retry |	|
|OnError를 받아 에러 처리를 하고 다른 스트림으로 전환한다. | Catch | |	
|OnError을 받아 에러 처리를 한 후, OnError을 무시하고 OnCompleted로 대체하고 싶다.	|CatchIgnore |	
|OnError가 오면 에러 처리를 한 후, Subscribe를 다시 하고 싶다. (시간 지정 가능)	|OnErrorRetry |

## "OnCompleted" 메시지

OnCompleted는 **"스트림이 완료되었기 때문에 이후 메시지를 발행하지 않겠다"**라는 것을 통지하는 메시지입니다.

만약 OnCompleted 메시지가 Subscribe까지 도달한 경우 OnError과 마찬가지로 그 스트림의 구독은 종료되고 파기됩니다. 이 성질을 이용하여 스트림에게 OnCompleted를 적절히 발행하여 올리면 정리하여 구독 종료를 실행할 수 있기 때문에 스트림 뒷정리를 할 경우에는 이 메시지를 발행하도록 합시다.

또한, 한번 OnCompleted를 발행한 Subject는 재이용이 불가능합니다. Subscribe 해도 금방 OnCompleted가 돌아오게 됩니다.

### 예 6. OnCompleted를 감지

```cs
var subject = new Subject<int>();
subject.Subscribe(
    x => Debug.Log(x),
    () => Debug.Log("OnCompleted")
);
subject.OnNext(1);
subject.OnNext(2);
subject.OnCompleted();
```

실행결과

    1
    2
    OnCompleted

※ Subscribe의 오버로드 중 Subscribe(OnNext, OnCompleted)을 받는 메서드를 이용하고 있습니다.

예 6과 같이 Subscribe에 OnCompleted를 받은 오버로드를 사용하여 OnCompleted를 감지할 수 있습니다.

## Subscribe의 오버로드

[UniRx 입문 그 1]({% post_url 2019-11-03-UniRx-Getting-Started-1 %}) 에서 Subscribe에는 여러 오버로드가 존재한다고 설명했습니다.

실제로는 다음 조합의 오버로드가 준비되어 있고, 이용하고 싶은 메시지에 맞게 선택하면 좋을 것 같습니다.

- Subscribe (IObserber observer) ----------- 기본형
- Subscribe () ------------------------------ 모든 메시지를 무시
- Subscribe (Action onNext) ---------------- OnNext 만
- Subscribe (Action onNext, Action onError) ---- OnNext + OnError
- Subscribe (Action onNext, Action onCompleted) - OnNext + OnCompleted
- Subscribe (Action onNext, Action onError, Action onCompleted) - 전부

## 2. 스트림의 구독 종료 (Dispose)

이어서 IObservable의 "IDisposable"를 설명 하겠습니다.
```CS
public interface IObservable<T>
{
    IDisposable Subscribe(IObserver<T> observer);
}
```

[IDisposable](https://docs.microsoft.com/ko-kr/dotnet/api/system.idisposable?view=netframework-4.8)는 C#에서 제공하는 인터페이스이며, "리소스 해제"를 실시할 수 있도록 하기 위한 메서드 "Dispose()"를 단지 1개 가지고 있는 인터페이스 입니다. 

Subscribe의 반환값이 IDisposable로 이라는 것은 즉 **Subscribe 메서드가 돌려주는 IDisposable의 Dispose를 실행하면 스트림의 구독을 종료 할수 있다**는 것이 됩니다.

### 예 7. Dispose() 스트림의 구독 종료

```cs
var subject = new Subject<int>();

// IDispose 저장
var disposable = subject.Subscribe(x => Debug.Log(x), () => Debug.Log("OnCompleted"));

subject.OnNext(1);
subject.OnNext(2);

// 구독종
disposable.Dispose();

subject.OnNext(3);
subject.OnCompleted();
```

실행결과

    1
    2

예 7은 구독을 Dispose를 호출하여 도중에 중지하는 예입니다.

따라서 Dispose를 호출하여 구독을 언제라도 중단 할 수 있습니다.

여기서 주의해야 할 것은 **Dispose()를 실행해서 구독이 중단되도 OnCompleted가 발행되는 것은 아니다** 라는 점입니다. 구독 중단 처리를 OnCompleted에 사용하는 경우, Dispose에서 정지시켜 버리면 실행되지 않으므로 주의하시기 바랍니다.

### 예 8. 특정 스트림만 수신 거부

```cs
var subject = new Subject<int>();

// IDispose 저장
var disposable1 = subject.Subscribe(x => Debug.Log("스트림1:" + x), () => Debug.Log("OnCompleted"));
var disposable2 = subject.Subscribe(x => Debug.Log("스트림2:" + x), () => Debug.Log("OnCompleted"));
subject.OnNext(1);
subject.OnNext(2);

// 스트림1만 구독종료
disposable1.Dispose();

subject.OnNext(3);
subject.OnCompleted();
```

실행결과

    스트림1 : 1
    스트림2 : 1
    스트림1 : 2
    스트림2 : 2
    스트림2 : 3
    2 : OnCompleted

예 8은 특정 Subscribe의 Dispose를 호출하여 그 구독만 중지시키고 있는 예입니다.

OnCompleted를 실행하면 모든 스트림을 구독 종료하게되지만 Dispose를 사용하면 일부 스트림만 종료시킬 수 있습니다.

## 3. 스트림의 수명과 Subscribe 종료 타이밍

UniRx를 사용하는데 있어서 특히 조심하지 않으면 안되는 것이 스트림의 라이프 사이클입니다.

객체가 자주 출현과 삭제를 반복하는 Unity에서는, 특히 이것을 의식하지 않으면 퍼포먼스 저하나 에러에 의한 오동작을 일으키게 됩니다.

## '스트림'의 실체는 누가 가지고 있는가?

스트림의 수명 관리를 하는데, "그 스트림은 누구의 소요인가?"를 의식할 필요가 있습니다.

기본적으로, 스트림의 실체는 **"Subject"**이며 Subject가 파기되면 스트림도 파기 됩니다.

![](/images/unity3d/2019-11-06-1.jpeg)

이전에도 설명했지만, "Subscribe"란 Subject에 함수를 등록하는 과정이었습니다.즉, 스트림의 실체는 Subject가 내부에 유지하는 "호출 함수 목록( 및 그 함수에 연관된 메소드 체인)"로, Subject가 스트림을 관리하는 것입니다.

Subject가 파기되면 스트림도 모두 파기됩니다. 반대로 말하면, **Subject가 남아있는 한 스트림은 계속 실행 된다** 인것입니다. 스트림이 참조하고 있는 객체를 스트림보다 먼저 버리고 방치해 버리면 뒤에서 스트림이 계속 동작 상태가 되기 때문에 성능 저하를 일으키거나 메모리 누수가 발생하거나 NullReferenceException을 발생시켜 게임을 정지시킬 가능성도 있습니다.

스트림의 수명 관리는 세심한 주의를 기울여야 됩니다. 사용이 끝나면 반드시 Dispose를 호출하거나 OnCompleted를 발행하는 습관을 들입시다.

### 예 9. 플레이어의 좌표를 이벤트 알림으로 갱신

요약

액션 게임을 상정하고 이런 경우를 생각해 봅시다.

- 플레이어를 조작 할 수 있다.
- 타이머로 시간을 카운트 하고 있다.
- 타이머가 0이 되었을 때 플레이어를 초기 좌표로 되돌린다.
- 플레이어는 화면 밖으로 나오면 사망(소멸)한다.

구현

```cs
using System;
using System.Collections;
using UniRx;
using UnityEngine;

/// <summary>
/// 카운트 다운하고 그때 값을 통지한다.
/// 3,2,1,0,(OnCompleted) 이런식으로 이벤트가 날라간다.
/// </summary>
public class TimeCounter : MonoBehaviour
{
    [SerializeField] private int TimeLeft = 3;
    
    // 타이머 스트림의 실체는 이 Subject
    private Subject<int> timerSubject = new Subject<int>();

    public IObservable<int> OnTimeChanged => timerSubject;

    private void Start()
    {
        StartCoroutine(TimerCoroutine());
        
        // 현재의 카운트를 표시
        timerSubject.Subscribe(x => Debug.Log(x));
    }

    private IEnumerator TimerCoroutine()
    {
        yield return null;

        var time = TimeLeft;
        while (time >= 0)
        {
            timerSubject.OnNext(time--);
            yield return new WaitForSeconds(1);
        }
        timerSubject.OnCompleted();
    }
}
```

```cs
using UniRx;
using UnityEngine;

// 플레이어 이동 처리
// 타이머가 0이되면 초기 좌표로 돌린다.
public class PlayerMover : MonoBehaviour
{
    [SerializeField] private TimeCounter _timeCounter;
    private float _moveSpeed = 10.0f;

    private void Start()
    {
        // 타이머 구독
        _timeCounter.OnTimeChanged
            .Where(x => x == 0) // 타이머가 0이 되었을 때만 실행
            .Subscribe(_ =>
            {
                // 타이머가 0이되면 초기 좌표로 돌린다
                transform.position = Vector3.zero;
            });
    }

    private void Update()
    {
        // 오른쪽 화살표를 누르고 있는 동안 이동
        if (Input.GetKey(KeyCode.RightArrow))
        {
            transform.position += new Vector3(1, 0, 0) * _moveSpeed * Time.deltaTime;
        }
        
        // 화면 밖으로 나오면 제거
        if (transform.position.x > 10)
        {
            Debug.Log("화면 밖에 나왔다!");
            Destroy(gameObject);
        }
    }
}
```

**실행 결과 (타이머 0에서 플레이어가 생존 한 경우)**

![](/images/unity3d/2019-11-06-2.gif)

타이머 0이 되었을 때 플레이어의 좌표가 제대로 초기 위치로 이동하게 됩니다.

이와 같이 플레이어가 생존한 경우는 이 코드가 제대로 실행됩니다.

**실행 결과 (타이머 0에서 플레이어가 제거 된 경우)**

![](/images/unity3d/2019-11-06-3.gif)

타이머 0의 시점에서 플레이어가 제거 된 경우 이 코드에서는 `MissingReferenceException` 예외가 발생하고 있습니다.

즉, 이 코드는 타이머 0 시점에서 플레이어가 소멸한 경우 이상이 있다는 것을 알 수 있습니다.

**원인**

원인은 `PlayerMover` 이 부분에 있습니다.

```cs
private void Start()
{
    // 타이머 구독
    _timeCounter.OnTimeChanged
        .Where(x => x == 0) // 타이머가 0이 되었을 때만 실행
        .Subscribe(_ =>
        {
            // 타이머가 0이되면 초기 좌표로 돌린다
            transform.position = Vector3.zero;
        });
}
```

아까 말한대로 스트림의 실체는 Subject가 보관 유지하고 있습니다.

**PlayerMover가 삭제 된다해도 TimeCounter.timerSubjec가 transform.position = Vector3.zero;를 호출** 하게 됩니다.

그리고 Player의 transform에 액세스하려고 하고 transform을 가져오는데 실패 해서 `MissingReferenceException` 가 발생해 버리는게 예외의 원인입니다.

![](/images/unity3d/2019-11-06-4.jpeg)

이와 같이 스트림의 수명과 객체의 수명이 일치하지 않는 경우, 스트림을 제대로 제거 하지 않는 경우 에러의 원인이 되어 버립니다.

**대책**

대책은 단순히 **Player의 GameObject가 파기되면 스트림의 구독을 중지하면 된다** 입니다.

구현 방법에는 여러 가지가 있지만, 이번에는 가장 간단한 `AddTo`의 사용 예제를 살펴 보도록 합니다.

```cs
private void Start()
{
    // 타이머 구독
    _timeCounter.OnTimeChanged
        .Where(x => x == 0) // 타이머가 0이 되었을 때만 실행
        .Subscribe(_ =>
        {
            // 타이머가 0이되면 초기 좌표로 돌린다
            transform.position = Vector3.zero;
        }).AddTo(gameObject); // 지정된 gameObject가 파기되면 Dispose 한다.
}
```

UniRx에는 AddTo 라는 메서드가 준비되어 있으며, Subscribe의 뒤에 AddTo(gameObject)라고 적음으로써 **지정된 GameObject가 Destroy되면 자동으로 Dispose를 호출**하도록 설정할 수 있습니다.

이렇게함으로써 Player가 제거되면 동시에 스트림의 구독을 중지하므로 조금 같은 예외가 발생하지 않습니다.

![](/images/unity3d/2019-11-06-5.gif)

화면을 나가서 객체가 삭제 되어도 예외가 발생하지 않게 되었다.

## 4.정리

**메시지의 종류는 3 종류가 존재한다.**

- **OnNext:** 보통 이벤트가 발생했을때 알림 메시지
- **OnError:** 스트림을 처리하는 동안 예외가 발생한 경우 통지한다.
- **OnCompleted:** 스트림이 종료되었음을 알린다.

**스트림의 구독을 중단하는 패턴은 3가지가 있다.**

- Subscribe가 OnCompleted를 감지
- Subscribe가 OnError를 감지
- Subscribe가 반환한 IDisposable의 Dispose()를 호출

**스트림의 수명과 객체의 수명 관계는 항상 의식할 필요가 있다.**

- 객체를 지웠지만, 스트림은 살아 남은 상태는 절대적으로 피해야 한다.
- 스트림을 사용한 후에 Dispose를 호출하거나 OnCompleted를 발행하는 버릇을 붙이자.