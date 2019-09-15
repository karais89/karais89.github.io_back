---
layout: post
title: "UniRx를 사용하여 동시에 화면에 비친 객체의 수를 세기"
excerpt: "UniRx를 사용하여 동시에 화면에 비친 객체의 수를 세기 번역"
date: 2019-09-14 13:39:00 +0900
tags: [unity3d, unirx]
---
* TOC
{:toc}

## 환경

---

- macOS Mojave v10.14.6
- Unity 2019.2.5f1
- Github Desktop
- Rider 2019.2
- UniRx v7.1.0

원문 : [https://qiita.com/toRisouP/items/83fd28b6d4a70a7ed1d2](https://qiita.com/toRisouP/items/83fd28b6d4a70a7ed1d2)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

## Reactive Extensions이란?

---

ReactiveExtensions (Rx)는 한마디로 "이벤트 및 비동기 처리에 LINQ와 같은 기술을 적용할 수 있게 하는 C# 라이브러리"입니다. 리액티브 프로그래밍에 대한 설명은 [당신이 놓치고 있던 리액티브 프로그래밍에 대한 안내](https://gist.github.com/casamia918/93b8db69beb9ee06b92a96b2a234d48e)를 보시면 쉽게 이해하실 수 있을 것이라고 생각합니다.

Rx는 원래 Unity에서는 작동하지 않습니다만, [neuecc님이 GitHub에 공개한 UniRx](https://github.com/neuecc/UniRx)는 Unity에서 Rx가 작동할 수 있게 만들었습니다. 이번 포스트에서는 UniRx를 사용한 스크립트를 소개하려고 합니다.

## 값을 통지하는 Subject<T> / 화면에 객체가 찍혔음을 통지하는 예제

---

Subject<T>를 사용하여 값을 발행하고 통지할 수 있습니다.

이것은 Event의 상위 호환 기능에 있어서, EventArgs에서 파라미터를 통지하는 것과 같이 감시하고 있는 대상에 값을 통지 할 수 있습니다.

그러면 Subject<T>를 사용해 실제로 카메라에 객체가 찍혔을 때에 통지되도록 해봅니다.

![](/images/unity3d/2019-09-14-1.png)

OnVisibleScript.cs
```csharp
using System;
using UniRx;
using UnityEngine;

/// <summary>
/// 게임오브젝트가 카메라에 찍힌 것을 통지하는 스크립트
/// </summary>
public class OnVisibleScript : MonoBehaviour
{
    /// <summary>
    /// 카메라에 비친 게임오브젝트를 흐르는 스트림
    /// </summary>
    private Subject<GameObject> onVisibleStream = new Subject<GameObject>();

    /// <summary>
    /// 외부에 공개하는 Observable
    /// </summary>
    public IObservable<GameObject> OnVisibleObservable => onVisibleStream.AsObservable();
    
    /// <summary>
    /// 카메라에 찍힐 때 실행되는 Unity 전용 콜백
    /// </summary>
    private void OnBecameVisible()
    {
        // OnNext에서 자신의 게임오브젝트를 스트림에 흐르게 한다.
        onVisibleStream.OnNext(gameObject);
    }
}
```

ObserveScript.cs
```csharp
using UniRx;
using UnityEngine;

/// <summary>
/// 대상을 감시하는 측면
/// </summary>
public class ObserveScript : MonoBehaviour
{
    // 관측 대상의 GameObject
    public GameObject targetCube;

    private void Start()
    {
        // OnVisibleScript를 획득
        var targetOnvisibleScript = targetCube.GetComponent<OnVisibleScript>();
        
        // Subscribe에서 값을 구독한다.
        targetOnvisibleScript.OnVisibleObservable
            .Subscribe(Debug.Log);
    }
}
```
이제 OnVisibleScript가 할당된 게임오브젝트가 카메라에 찍힌 순간에 OnVisibleObservable의 값이 흐릅니다. OnVisibleObservable을 모니터링하는 ObserveScript가 그것을 감지하고 Debug.Log가 실행됩니다.

OnNext()는 값을 통지합니다. "Event를 발화하는 것(Invoke)" 것과 같은 처리에 해당됩니다.

Subscribe()는 기존의 Event에서 "EventHandler 등록"에 해당합니다.

Event를 사용하는 경우 delegate를 정의하거나 EventArgs을 정의하는 등 복잡한 처리가 많았습니다. UniRx는 Subject를 정의하는 것만으로 매우 간결하게 쓸 수 있습니다.

## 이벤트를 합성 해보자 (merge) /  화면에 동시에 찍힌 숫자를 세어 보는 예제.

---

Subject<T>를 사용하면 이벤트 통지를 간단하게 쓸 수 있다고 했지만, 그것이 Rx의 전부는 아닙니다.

이 이벤트 통지를 합성, 필터링, 투영 등 유연한 처리를 실시 할 수 있습니다.

예를 들어, "카메라가 이동하여 화면에 객체가 찍혔을 때, 동시에 몇 개의 객체가 카메라에 찍혔는지를 계산한다"를 처리하고 싶다고 가정 합니다.

우선은 사전 준비한 OnVisibleScript를 붙인 Target Cube를 씬 상에 몇개를 배치 하고 Cubes라는 오브젝트의 자식으로 정리해 둡니다.

![](/images/unity3d/2019-09-14-2.png)

다음에, 조금전의 ObserveScript.cs를 다시 씁니다.
```csharp
using System;
using System.Linq;
using UniRx;
using UnityEngine;

public class ObserveScript : MonoBehaviour
{
    /// <summary>
    /// TargetCube를 묶는 GameObject
    /// UnityEditor에서 설정해두자.
    /// </summary>
    public GameObject cubes;

    private void Start()
    {
        // OnVisibleScript를 획득
        var onVisibleScripts = cubes.GetComponentsInChildren<OnVisibleScript>();
        
        // Merge : 여러개의 OnVisibleObservable을 하나로 통합
        var allOnVisibleObservable = Observable.Merge(onVisibleScripts.Select((x => x.OnVisibleObservable)));
        
        // 250ms 이내에 화면에 함께 찍힌 GameObject를 계산
        allOnVisibleObservable
            .Buffer(allOnVisibleObservable.Throttle(TimeSpan.FromMilliseconds(250)))
            .Subscribe(x => Debug.Log(x.Count));
    }
}
```

이상입니다. 몇 줄 고쳐 쓴 것만으로 "동시에 화면에 찍힌 객체의 수를 세다"는 복잡한 처리를 할 수 있었습니다.

![](/images/unity3d/2019-09-14-3.png)

9개가 동시에 화면에 비치기 때문에 "9으로 표시되어 있습니다.

하나 하나 무엇을 하고 있는지 설명하겠습니다.

우선 Observable.Merge()를 사용하여 여러 스트림을 하나의 스트림인 allOnVisibleObservable로 합성하고 있습니다.

Merge
```csharp
// Merge : 여러개의 OnVisibleObservable을 하나로 통합
var allOnVisibleObservable = Observable
        .Merge(onVisibleScripts.Select((x => x.OnVisibleObservable))); 
```

![](/images/unity3d/2019-09-14-4.jpeg)

이어 Buffer를 사용하여 값의 통지를 막습니다.

Buffer는 이벤트를 컬렉션으로 정리하고, 해제 이벤트가 올때 까지 계속 막습니다. Buffer의 해제는 Throttle를 사용해 생성합니다.

Throttle은 **마지막으로 값이 와서 일정 기간 경과**하면 발화하는 것입니다.

이번에는 **마지막에 값이 와서 250ms 초 이상 경과하면 Buffer를 해제** 했습니다. (즉, 250ms 이내에 화면에 비친 것은 동시에 비친 것으로 계산 됩니다)

buffer
```csharp
// 250ms 이내에 화면에 함께 찍힌 GameObject를 계산
allOnVisibleObservable
    .Buffer(allOnVisibleObservable.Throttle(TimeSpan.FromMilliseconds(250)))
    .Subscribe(x => Debug.Log(x.Count));
```

![](/images/unity3d/2019-09-14-5.jpeg)

이 외에도 Where에서 특정 이벤트만을 필터링 하거나 Select에서 흘러 나오는 값 자체를 바꿔거나 Rx를 사용하는 것으로 LINQ와 비슷한 방식으로 이벤트를 유연하게 취급할 수 있게 됩니다.