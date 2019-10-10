---
layout: post
title: "Update()을 Observable로 변환하는 방법"
excerpt: "Update()을 Observable로 변환하는 방법 번역"
date: 2019-10-10 22:41:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/972b97367df12c3457d2](https://qiita.com/toRisouP/items/972b97367df12c3457d2)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

UniRx를 사용하다보면 Update()를 Observable로 변환하여 사용하는 경우가 많습니다.

이번에는 그 Update를 Observable로 변환하는 방법을 소개하고 싶습니다.

## 주의

**본 포스트보다 더 많은 내용이 상세하게 작성되어 있는 포스트가 있으므로 여기를 참조하십시오.**

[UniRx 입문 4 - Update를 스트림으로 변환하는 방법 및 장점 -](https://qiita.com/toRisouP/items/30c576c7b0a99f41fb87)

## 이하 오래된 설명

## Update → Observable로 변환하기

Update를 Observable로 변환하는 방법은 2015/04/10 시점에서 3종류가 있습니다.

- ObservableMonoBehaviour & UpdateAsObservable
- ObservableUpdateTrigger & UpdateAsObservable
- Observable.EveryUpdate

각각 미묘하게 다르므로 사용하는데 주의가 필요합니다.

## ObservableMonoBehaviour & UpdateAsObservable

**[주의] UniRx 4.8 이후 버전 부터는 ObservableMonoBehaviour의 사용이 비추천이 되었습니다.**

[역주] UniRx 6.0.0 이후 버전 부터는 ObservableMonoBehaviour 스크립트 자체가 제거 되었습니다.

```csharp
public class UniRxSample : ObservableMonoBehaviour
{
    public override void Start()
    {
        base.Start();

        UpdateAsObservable()
            .Subscribe(_ => Debug.Log("Update!"));
    }

    public override void Update()
    {
        // Update를 override하면 base.Update ()의 호출을 잊지
        base.Update();   
    }
}
```

**특징**

- ObservableMonoBehaviour는 MonoBehaviour 파생 클래스에서 **상속하여 사용할 필요가 있다.**
- UpdateAsObservable()는 Update 타이밍에 통지된다.
- Component가 삭제되면 자동으로 Dispose가 호출된다.
- 스트림은 IObservable<Unit> 형태이다.
- ObservableMonoBehaviour에는 Update 이외의 통지도 준비되어 있으므로 편리하다.
- Script Execution Order에서 다른 스트립트와 실행 순서를 세세하게 지정할 수 있다.

**설명**

UpdateAsObservable는 내부적으로 Subject를 가지고 있으며, Update()시 OnNext를 호출하는 단순한 구조로되어 있습니다. ([해당란](https://github.com/neuecc/UniRx/blob/4.8.0/Assets/UniRx/Scripts/UnityEngineBridge/ObservableMonoBehaviour.cs#L758-L761))

따라서 Update를 Override할 때 base.Update()의 호출을 제대로 추가해주지 않으면, UpdateAsObservable()는 작동하지 않기 때문에 주의가 필요합니다.

~~또한 ObservableMonoBehaviour는 다양한 이벤트 트리거가 포함되어 있으며 이를 상속 하면 편하게 사용이 가능합니다. 하지만 ObservableMonoBehaviour 상속할 수 없는 장면이나 의도적으로 상속하지 않으려는 경우에는 사용할 수 없습니다.~~

### [추기]

**제작자 [@neuecc](https://qiita.com/neuecc)님이 말하길 ObservableMonoBehaviour을 사용하는 것은 비추천이며, UniRx 4.8 이상에서는 지원되지 않습니다.**

**대신 아래의 ObservableUpdateTrigger를 사용 합시다.**

## ObservableUpdateTrigger

```csharp
using UniRx.Triggers;
using UniRx;
using UnityEngine;

public class UniRxSample : MonoBehaviour
{
    private void Start()
    {
        this.UpdateAsObservable()
            .Subscribe(_ => Debug.Log("Update!"));
    }
}
```

**특징**

- ObservableUpdateTrigger는 UniRx.Triggers 네임스페이스에 정의되어 있습니다.
- **UniRx.Triggers를 Using에 선언해두면 UpdateAsObservable()를 직접 호출할 수 있습니다.**
- 실제 동작은 ObservableUpdateTrigger에 있습니다.
- 호출시에 내부적으로 ObservableUpdateTrigger가 AddComponent 됩니다. (실제로 사용할 때는 Trigger의 존재는 신경 쓰지 않아도 됩니다.)
- ObservableMonoBeaviour과 내부 구조는 동일 합니다.

ObservableUpdateTrigger는 이벤트 트리거마다 분할한 것 중 1개 입니다.

ObservableMonoBehaviour은 상속이 필요했지만, 이쪽은 단지 Using을 추가 할 뿐이므로 사용성이 높습니다.

## Observable.EveryUpdate

```csharp
Observable
        .EveryUpdate()
        .Subscribe(x => Debug.Log(x));
```

**특징**

- static 메서드로 정의되어 있기 때문에 MonoBehaviour 이외의 장소에서 사용할 수 있다.
- 스트림에는 Subscribe 하고 나서의 프레임 수가 흘러 나온다.
- **Subscribe의 Dispose는 수동으로 호출 해야 한다.** (또는 [AddTo]({% post_url 2019-10-10-UniRx-Connect-the-Dispose-of-the-Subscribe-to-the-GameObject %})를 사용하여 GameObject에 연결할 필요가 있다.)

**설명**

Observable.EveryUpdate는 static 메서드로 정의되어 있기 때문에 MonoBehaviour 이외의 장소에서도 사용할 수 있습니다. 내부적으로 [MainThreadDispatcher](https://github.com/neuecc/UniRx/tree/7.1.0#microcoroutine)의 코루틴의 실행 타이밍에 통지 됩니다.

참고로, Subscribe의 IDisposable을 올바르게 관리해야 하여, 사용시에는 주의가 필요합니다. (AddTo 등을 잊지 않고 붙여 둘 필요가 있습니다.)

## 정리

- 빨리 ~~사용한다면 ObservableMonoBehaviour를 상속하는 형태를 사용~~ ObservableMonoBehaviour은 비추천 되었습니다.
- **UniRx.Triggers을 Using 추가 UpdateAsObservable()를 직접 호출하는 것이 가장 간단하고 사용하기 쉽습니다.**
- Observable.EveryUpdate는 사용하는 곳이 별로 없습니다.