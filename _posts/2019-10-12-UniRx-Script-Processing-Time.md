---
layout: post
title: "스크립트 처리 시점을 조작한다"
excerpt: "스크립트 처리 시점을 조작한다 번역"
date: 2019-10-12 09:04:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/e402b15b36a8f9097ee9](https://qiita.com/toRisouP/items/e402b15b36a8f9097ee9)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

---

Unity로 게임 개발을 하고 있으면 **"어떤 처리를 일정시간 후나 특정 타이밍에 실행하고 싶다"**라고 하는 일이 자주 있기 때문에, 방법을 정리해 보았습니다.

# Unity의 표준 기능만 사용하는 경우

## 처리를 n초 후에 실행하고 싶다

### [Invoke](https://docs.unity3d.com/ScriptReference/MonoBehaviour.Invoke.html)를 사용
```csharp
private void Start()
{
    // DelayMethod을 3.5 초 후에 호출 
    Invoke("DelayMethod", 3.5f);
}

private void DelayMethod()
{
    Debug.Log("Delay call");
}
```

아마 가장 간단한 방법일 것입니다.

Invoke를 사용하는 것으로 지정한 메서드를 n초 후에 실행시킬 수 있습니다.

그러나 메서드를 문자열로 지정하지 않으면 안되고, 메서드에 인수를 전달할 수 없는 등 쓰기가 불편한 부분이 있습니다.

# 코루틴을 사용

```csharp
private void Start()
{
    // 3.5 초 후에 실행
    StartCoroutine(DelayMethod(3.5f, () =>
    {
        Debug.Log("Delay Call");
    }));
}

/// <summary>
/// 전달 된 처리를 지정 시간 이후에 실행 한다.
/// </summary>
/// <param name="waitTime">지연시간[밀리초]</param>
/// <param name="action">수행할 작업</param>
/// <returns></returns>
private IEnumerator DelayMethod(float waitTime, Action action)
{
    yield return new WaitForSeconds(waitTime);
    action();
}
```

코루틴과 [WaitForSeconds](https://docs.unity3d.com/ScriptReference/WaitForSeconds.html)를 함께 쓰는 방식.

Invoke와 달리 타입에 안전하고, 인수도 줄 수 있어 이쪽을 사용하는 것이 더 좋다고 생각 합니다.

MonoBehaviour에 [확장 메서드](https://unity3d.com/kr/learn/tutorials/topics/scripting/extension-methods)를 추가하면 유용 합니다.

## n 프레임 후에 실행하고 싶다

### 코루틴을 사용

```csharp
private void Start()
{
    // 5 프레임 후에 실행
    StartCoroutine(DelayMethod(5, () =>
    {
        Debug.Log("Delay Call");
    }));
}

/// <summary>
/// 전달된 처리를 지정 프레임 이후에 실행
/// </summary>
/// <param name="delayFrameCount">지연할 프레임</param>
/// <param name="action">수행 할 작업</param>
/// <returns></returns>
private IEnumerator DelayMethod(int delayFrameCount, Action action)
{
    for (int i = 0; i < delayFrameCount; i++)
    {
        yield return null;
    }

    action();
}
```

코루틴에서 n초 기다리는 것과 크게 다르지 않습니다.

이것도 확장 메서드로 추가하면 유용 합니다.

# UniRx를 사용하는 경우

## n 초 후에 실행하고 싶다

### Observable.Timer을 사용

```csharp
// 단지 호출만 하는 경우
// 100 밀리 초 후에 Log를 출력한다.
Observable.Timer(TimeSpan.FromMilliseconds(100))
    .Subscribe(_ => Debug.Log("Delay call"));

// 매개 변수를 전달하는 경우
// 현재 플레이어의 좌표를 500 밀리 초 후에 표시
var playerPosition = transform.position;
Observable.Timer(TimeSpan.FromMilliseconds(500))
    .Subscribe(_ => Debug.Log("Player Position : " + playerPosition));
```

Timer을 사용하는 경우.

매개 변수를 전달하려고 하는 경우에는 스트림 밖에서 값의 유지가 필요하므로 동시에 여러 타이머에 등록하면 문제가 생길 수 있습니다.

### Delay를 사용

```csharp
// 단지 호출하는 경우
// 100 밀리 초 후에 Log를 출력한다.
Observable.Return(Unit.Default)
    .Delay(TimeSpan.FromMilliseconds(100))
    .Subscribe(_ => Debug.Log("Delay call"));

// 매개 변수를 전달하는 경우
// 현재 플레이어의 좌표를 500 밀리 초 후에 표시
Observable.Return(transform.position)
    .Delay(TimeSpan.FromMilliseconds(500))
    .Subscribe(p => Debug.Log("Player Position : " + p));
```

## n 프레임 후에 실행하고 싶다

### Observable.TimerFrame를 사용

```csharp
// 다음 프레임에서 실행
Observable.TimerFrame(1)
    .Subscribe(_ => Debug.Log("Next Update"));

// 다음 FixedUpdate에서 실행
Observable.TimerFrame(1, FrameCountType.FixedUpdate)
    .Subscribe(_ => Debug.Log("Next FixedUpdate"));
```

Observable.Timer와 크게 다르지 않다.

### DelayFrame를 사용

```csharp
// 다음 프레임에서 실행
Observable.Return(Unit.Default)
    .DelayFrame(1)
    .Subscribe(_ => Debug.Log("Next Frame"));

// 다음 FixedUpdate에서 실행
Observable.Return(Unit.Default)
    .DelayFrame(1, FrameCountType.FixedUpdate)
    .Subscribe(_ => Debug.Log("Next FixedUpdate"));
```

Delay와 크게 다르지 않다.

### 다음 프레임에서 실행

다음 프레임에서 실행하고 싶다면 NextFrame를 사용하는 것이 더 스마트한 방법이다.

```csharp
// 다음 프레임에서 실행
Observable.NextFrame()
          .Subscribe(_ => Debug.Log("Next Frame"));
```

# 정리

단지 처리를 지연 시키고 싶다면 코루틴을 쓰는 것이 편합니다.

하지만 처리를 지연시킨 후, 아직 뭔가 처리를 계속 해야 되는 경우라면 UniRx를 사용하여 스트림화 하는 것이 여러모로 쉽다고 생각합니다.