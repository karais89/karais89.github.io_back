---
layout: post
title: "UniRx 입문 그 4 -Update를 스트림으로 변환하는 방법 및 장점 -"
excerpt: "UniRx 입문 그 4 -Update를 스트림으로 변환하는 방법 및 장점 - 번역"
date: 2020-02-23 19:46:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/30c576c7b0a99f41fb87](https://qiita.com/toRisouP/items/30c576c7b0a99f41fb87)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

[UniRx 입문 시리즈 목차는 이쪽]({% post_url 2019-11-17-UniRx-Getting-Started-List %})

---

# 0. 이전 복습

이전에는 스트림의 구축 방법을 몇 가지 소개 했습니다. 이번에는 더 실용성이 높은 "Update를 변환하는 방법"에 중점을 두고 설명 하겠습니다.

이 포스트에 적은 내용은 과거에 적은 [[UniRx] Update()를 Observable로 변환하는 방법]({% post_url 2019-10-10-UniRx-How-to-convert-Update-to-Observable %})의 내용을 고쳐 쓴 내용 입니다.

# 1. Update()를 스트림으로 변환하는 방법

Unity의 Update() 호출을 스트림으로 변환하는 방법은 두 가지가 있습니다.

- UniRx.Triggers의 UpdateAsObservable을 이용하는 방법
- Observable.EveryUpdate를 이용하는 방법

위 두 방법은 동작 자체는 비슷하지만 내부 구현이 크게 다릅니다. 우선 각각의 사용법과 구조를 설명하겠습니다.

## UniRx.Triggers의 UpdateAsObservable를 이용하는 방법

### 사용법

**호출 방법**

1. `using UniRx.Triggers;` 추가
2. `this.UpdateAsObservable()` 선언

발행되는 형태

Unit
```cs
using UnityEngine;
using UniRx;
using UniRx.Triggers; // 이 using문이 필요

public class UpdateSample : MonoBehaviour
{
    private void Start() =>
        // UpdateAsObservable는 Component에 대한
        // 확장 메서드로 정의되어 있기 때문에 호출시
        // "this"가 필요
        this.UpdateAsObservable()
            .Subscribe(_ => Debug.Log("Update!"));
}
```
위처럼 Update 이벤트를 UniRx 스트림으로 변환하여 이용할 수 있습니다.

또한 `UpdateAsObservable`이 GameObject가 파괴되었을때 자동으로 OnCompleted가 발행되기 때문에 스트림의 수명 관리도 쉽습니다.

![](/images/unity3d/2020-02-23-15.png)

(Destroy시 OnCompleted가 발행된다)

### 구조

`UpdateAsObservable`은 `ObservableUpdateTrigger 컴포넌트`에 실체를 갖는 스트림 입니다.

`UpdateAsObservable`을 호출 하는 타이밍에 해당 GameObject에 `ObservableUpdateTrigger 컴포넌트`를 UniRx가 자동으로 연결하고 이 ObservableUpdateTrigger가 발행하는 이벤트를 사용하는 구조로 되어 있습니다.

UpdateAsObservable는 ObservableUpdateTrigger를 초기화하는 확장 메서드
```cs
public static IObservable<Unit> UpdateAsObservable(this Component component)
{
    if (component == null || component.gameObject == null) return Observable.Empty<Unit>();
    return GetOrAddComponent<ObservableUpdateTrigger>(component.gameObject).UpdateAsObservable();
}
```

UpdateAsObservable의 본체
```cs
using System; // require keep for Windows Universal App
using UnityEngine;

namespace UniRx.Triggers
{
    [DisallowMultipleComponent]
    public class ObservableUpdateTrigger : ObservableTriggerBase
    {
        Subject<Unit> update;

        /// <summary>Update is called every frame, if the MonoBehaviour is enabled.</summary>
        void Update()
        {
            if (update != null) update.OnNext(Unit.Default);
        }

        /// <summary>Update is called every frame, if the MonoBehaviour is enabled.</summary>
        public IObservable<Unit> UpdateAsObservable()
        {
            return update ?? (update = new Subject<Unit>());
        }

        protected override void RaiseOnCompletedOnDestroy()
        {
            if (update != null)
            {
                update.OnCompleted();
            }
        }
    }
}
```
(코드는 [여기](https://github.com/neuecc/UniRx/blob/7.1.0/Assets/Plugins/UniRx/Scripts/UnityEngineBridge/Triggers/ObservableUpdateTrigger.cs)에서 인용)

![](/images/unity3d/2020-02-23-16.png)

이처럼 UpdateAsObservable를 호출하는 것으로 `ObservableUpdateTrigger 컴포넌트`를 GameObject에 붙여 `ObservableUpdateTrigger`에서 실행되는 Update()를 내부에 가지는 Subject를 사용하여 단지 이벤트를 발행하고 있는 간단한 구조로 되어 있습니다.

여기서 주의해야 할 점은 다음 두가지 입니다.

- ObservableUpdateTrigger라는 수수께끼의 컴포넌트가 갑자기 증가하더라도 그것이 정상 동작이므로 삭제하지 말자
- 1개의 GameObject마다 1개의 ObservableUpdateTrigger를 공유하고 이용하기 때문에 UpdateAsObservable 자체를 무수히 Subscribe 해도 그다지 비용이 증가 될것은 없다.

특히 **컴포넌트가 갑자기 증가하더라도 그것이 정상 동작이므로 삭제하지 말자** 라는 점만 기억해두면 좋을 것 같습니다.

## Observable.EveryUpdate를 이용하는 방법

### 사용법

**호출 방법**

1. `Observable.EveryUpdate()` 직접 Subscribe하기

**발행되는 형태**

long(Subscribe 후 경과한 프레임)
```cs
using UniRx;
using UnityEngine;

public class UpdateSample : MonoBehaviour
{
    private void Start() =>
        Observable.EveryUpdate()
            .Subscribe(_ => Debug.Log("Update!"));
}
```

기본적으로 사용법은 이전의 `UpdateAsObservable`와 같습니다. 하지만 한 가지 큰 차이는, **Observable.EveryUpdate()는 스스로 OnCompleted를 발행하지 않습니다.** 즉 `Observable.EveryUpdate()`를 사용하는 경우 반드시 직접 스트림의 수명 관리를 해야 합니다.

### 구조

Observable.EveryUpdate()는 UniRx의 기능 중 하나 인 "마이크로코루틴"을 이용하여 동작하며, 구조는 UpdateAsObservable에 다소 복잡 합니다. 굉장히 관결하게 정리 하면 **"Observable.EveryUpdate()는 호출 될 때마다 싱글톤 상에서 코루틴을 시작 한다"**라는 동작으로 보면 됩니다. 이 코루틴은 수동으로 멈추지 않는 한 계속 실행되기 때문에 스트림의 수명 관리를 제대로 하지 않으면 [입문 2]({% post_url 2019-11-06-UniRx-Getting-Started-2 %})에서 언급한 문제를 일으킬 수 있습니다.

그러나 반면 `Observable.EveryUpdate()`에는 다음과 같은 장점도 존재합니다.

- **싱글톤에서 작동하기 때문에 게임 진행 내내 존재하는 스트림을 생성할 수 있다.**
- **대량의 Subscribe해도 성능이 저하되지 않는다. ([마이크로 코루틴](http://neue.cc/2016/05/14_529.html)의 성질)**

또한 UniRx가 관리하는 싱글톤은 "MainThreadDispatcher"라는 GameObject입니다. UniRx를 사용하고 있으면 어느새 생성될 수 있을 거라고 생각합니다. 이쪽도 UniRx의 동작에 절대적으로 필요하기 때문에 마음대로 삭제하거나 하지 않도록 주의 합시다.

![](/images/unity3d/2020-02-23-17.png)

(MainThreadDispatcher는 UniRx가 관리, 이용하고 있는 싱글톤 객체이다. 마음대로 삭제하지 말자)

## UpdateAsObservable()와 Observable.EveryUpdate()의 구분

이 두개는 동작은 비슷하지만 내부 구현은 크게 차이가 있었습니다. 각각의 작동 원리를 확실히 파악하고 각 상황에 따라 적절한 쪽을 이용하면 좋을 것 같습니다.

- **UpdateAsObservable():** GameObject가 파기되면 자동으로 멈춘다
- **Observable.EveryUpdate():** 성능상으로 이점이 있지만 Dispose를 수동으로 호출할 필요가 있다.

**UpdateAsObservable를 사용하면 좋을 것 같은 장소**

- **GameObject에 연관된 스트림을 이용한다.**
    - OnDestroy시 OnCompleted가 발행되므로 수명 관리가 편하다.

**Observable.EveryUpdate()를 사용하면 좋을 것 같은 장소**

- **GameObject를 이용하지 않는 Pure한 Class에서 Update 이벤트를 이용하고 싶을 때**
    - 싱글톤을 통해 Update 이벤트를 가져올 수 있으므로 MonoBehaviour를 상속하지 않아도 Update 이벤트를 사용할 수 있다.
- **게임 중에 항상 존재하고 작동하는 스트림을 준비하고 싶을 때**
    - 싱글톤을 사용하고 있기 때문에 OnCompleted가 자동으로 발동하지 않는다.
    - 참고: [UniRx에서 FPS 카운터 만들기]({% post_url 2019-10-23-UniRx-FPS-Counter %})
- **대량의 Update() 호출이 필요할 때**
    - 소량의 Update() 호출보다 압도적으로 성능이 나온다.

---

솔직히 어느 쪽을 사용해야 할 것인가는 선택의 문제이기도 하다 생각합니다. Observable.EveryUpdate() 쪽이 성능은 좋지만, Dispose를 해야 되는 단점이 있습니다. 에라가 나서 스트림이 멈춘다면 좋지만, 가장 무서운 것은 에러가 발생해도 뒤에서 계속 움직여버리는 경우입니다. 눈치 채보니 쓰레기 스트림이 뒤에서 대량으로 작동 하는 경우와 같습니다.

그래서 아무리 성능에 차이가 있다고 해도 그 성능 차이가 게임의 동작에 영향을 주는 상황이란 거의 없습니다. (엄청난 양의 GameObject를 동시에 생성하고 움직였을 때 라든지?) 때문에, 개인적으로 **더 안전한 UpdateAsObservable()을 사용하기를 권장합니다.**

# 2. Update를 스트림으로 변환하는 이유

UniRx를 이용해야 하는 이유중 1개는 "Update를 스트림으로 변환 할 수 있다"는 점이라고 생각합니다. 스트림 화하면 다음과 같은 이점이 있습니다.

- **UniRx 오퍼레이터를 이용하여 로직을 작성 할 수 있게 된다.**
- **로직의 처리 단위가 명확해진다.**

## 오퍼레이터를 이용한 로직의 작성

UniRx는 시간에 관련된 오퍼레이터가 다수 준비되어 있기 때문에 UniRx 스트림에서 논리를 작성하고 나면 시간의 관계 논리를 간결하게 기술 할 수 있습니다.

예를 들어, **버튼을 누르고 있는 동안 일정 간격으로 공격** 하는 처리를 생각해 봅시다.

버튼을 누르고 있는 동안 일정 간격으로 공격한다는 것은 예를 들어 슈팅 게임 총알의 발사 등으로 사용할 수 있습니다. "버튼을 누르고 있는 동안 n초마다 총알을 발사한다"라는 상황입니다.

이를 UniRx를 이용하지 않고 구현하는 경우 마지막으로 실행한 시간을 기록하여 매 프레임 비교하는 등 복잡하고 귀찮은 구현이 필요합니다. 하지만 UniRx를 사용하면 다음과 같이 구현할 수 있습니다.

```cs
using System;
using UniRx;
using UniRx.Triggers;
using UnityEngine;

public class UpdateSample3 : MonoBehaviour
{
    // 실행 간격
    [SerializeField]
    private float intervalSeconds = 0.25f;

    private void Start() =>
        // ThrottleFirst는 마지막으로 실행하고
        // 일정 시간 OnNext를 차단하는 오퍼레이터
        this.UpdateAsObservable()
            .Where(_ => Input.GetKey(KeyCode.Z))
            .ThrottleFirst(TimeSpan.FromSeconds(intervalSeconds))
            .Subscribe(_ => Attack());

    private void Attack() => Debug.Log("Attack");
}
```

이렇게 UniRx를 사용하면 컬렉션에 복잡한 처리를 LINQ에서 짧게 쓰는 것과 마찬가지로, 게임 로직을 **선언적으로 간결하게 작성하는 것이 가능**하다.

## 논리가 명확해진다

Unity에서 개발을 진행 하면, Update() 내에서 게임 로직이 담겨 엉망이 되어가는 경우가 대부분이라고 생각합니다.

그것도 UniRx를 사용하여 정리 할 수 있습니다.

### 이동, 점프, 착지시 효과음의 재생을 하는 로직의 예

이동, 점프, 착지시 효과음의 재생을 한다는 로직을 UniRx를 사용한 경우와 그렇지 않은 경우를 작성해 보겠습니다.

UniRx없이 작성
```cs
using System;
using UnityEngine;

public class Sample : MonoBehaviour
{
    private CharacterController characterController;

    // 점프 중 플래그
    private bool isJumping;

    void Start()
    {
        characterController = GetComponent<CharacterController>();
    }

    void Update()
    {
        if (!isJumping)
        {
            var inputVector = new Vector3(
                Input.GetAxis("Horizontal"),
                0,
                Input.GetAxis("Vertical")
            );

            if (inputVector.magnitude > 0.1f)
            {
                var dir = inputVector.normalized;
                Move(dir);
            }
            if (Input.GetKeyDown(KeyCode.Space) && characterController.isGrounded)
            {
                Jump();
                isJumping = true;
            }
        }
        else
        {
            if (characterController.isGrounded)
            {
                isJumping = false;
                PlaySoundEffect();
            }
        }


    }

    void Jump()
    {
        // Jump 처리
    }

    void PlaySoundEffect()
    {
        // 효과음 재생
    }

    void Move(Vector3 direction)
    {
        // 이동 처리
    }
}
```

UniRx를 사용하여 작성
```cs
using System;
using UniRx;
using UniRx.Triggers;
using UnityEngine;

public class Sample : MonoBehaviour
{
    private CharacterController characterController;

    // 점프 중 플래그
    private BoolReactiveProperty isJumping = new BoolReactiveProperty();

    void Start()
    {
        characterController = GetComponent<CharacterController>();

        // 점프 중이 아니면 이동
        this.UpdateAsObservable()
            .Where(_ => !isJumping.Value)
            .Select(_ => new Vector3(Input.GetAxis("Horizontal"), 0, Input.GetAxis("Vertical")))
            .Where(x => x.magnitude > 0.1f)
            .Subscribe(x => Move(x.normalized));

        // 점프 중이 아니라면 점프
        this.UpdateAsObservable()
            .Where(_ => Input.GetKeyDown(KeyCode.Space) && !isJumping.Value && characterController.isGrounded)
            .Subscribe(_ =>
            {
                Jump();
                isJumping.Value = true;
            });

        // 착지 플래그가 변화 할때 점프 중 플래그를 리셋
        characterController
            .ObserveEveryValueChanged(x => x.isGrounded)
            .Where(x => x && isJumping.Value)
            .Subscribe(_ => isJumping.Value = false)
            .AddTo(gameObject);

        // 점프 중 플래그가 false가 되면 효과음을 재생
        isJumping.Where(x => !x)
            .Subscribe(_ => PlaySoundEffect());
    }

    void Jump()
    {
        // Jump 처리
    }

    void PlaySoundEffect()
    {
        // 효과음 재생
    }

    void Move(Vector3 direction)
    {
        // 이동 처리
    }
}
```

위 두가지를 비교하면 어떻습니까?

UniRx를 사용하지 않는 경우 Update 내에서 여러 작업을 함께 작성해야 하기 때문에 if문에 의해 중첩이 발생하거나 변수의 범위가 모호해지는 등의 문제점이 있었습니다.

하지만, UniRx를 사용하여 Update를 스트림화 하는 경우 **로직 단위로 처리를 분할하고 나열해 기술할 수** 있게 되었고, 변수의 범위도 스트림 내에 닫힌 구현이 되었습니다.

이와 같이 Update를 스트림화하는 것으로 처리를 적절한 단위로 구분하여 기술할 수 있게 되며, 변수의 범위도 명확히 할 수 있습니다.

또한 ObserveEveryValueChanged 대해서는 뒤의 보충 내용을 참조하십시오.

# 3. 정리

**Update()를 스트림으로 변환하는 방법은 2가지**

- 일반적인 용도로 사용하는 경우 `UpdateAsObservable()`를 하면 된다.
- 특수 용도의 경우 `Observable.EveryUpdate()`를 사용 하면 된다.

**Update()를 스트림으로 변환하면 로직을 설명하기 쉬워진다.**

- UniRx 오퍼레이터를 게임로직에 그대로 사용할 수 있다.
- 선언적으로, 간결하고 읽기 쉽게 작성할 수 있게 된다.

# 4. 추가

## ObserveEveryValueChanged에 대해

```cs
var charcterController = GetComponent<CharacterController>();

// CharacterController의 IsGrounded을 감시
// false → true가 되면 로그출력
charcterController
    .ObserveEveryValueChanged(c => c.isGrounded)
    .Where(x => x)
    .Subscribe(_ => Debug.Log("착지!"))
    .AddTo(gameObject);

// ↑ 코드는 ↓와 거의 동의어
Observable.EveryUpdate()
    .Select(_=>charcterController.isGrounded)
    .DistinctUntilChanged()
    .Where(x=>x)
    .Subscribe(_ => Debug.Log("착지!"))
    .AddTo(gameObject);
```

지난번 `ObserveEveryValueChanged`는 `Observable.EveryUpdate` + `Select` + `DistinctUntilChanged`의 축약이라고 설명했습니다. 사실 이 설명은 미묘하게 잘못 되었습니다.

`ObserveEveryValueChanged`는 감시 대상의 오브젝트를 [약 참조(WeakReference)](https://docs.microsoft.com/ko-kr/dotnet/api/system.weakreference?view=netframework-4.8)에서 참조 합니다.

즉, **ObserveEveryValueChanged의 모니터링은 GC의 참조 카운트에 포함되지 않습니다.** 또한 ObserveEveryValueChanged는 감시 대상의 객체가 GC에 회수되면 **OnCompleted를 자동으로 발행** 합니다.

이 점에 유의하여 `ObserveEveryValueChanged`를 이용하면 좋을 것 같습니다.