---
layout: post
title: "마우스 롱클릭 판단하기"
excerpt: "마우스 롱클릭 판단하기 번역"
date: 2019-10-23 09:00:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/4fec0e9716be4d415798](https://qiita.com/toRisouP/items/4fec0e9716be4d415798)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

UniRx를 사용하여 마우스를 롱클릭을 판정하는 스트림을 만들어 보았습니다.

- 롱클릭 판정 (일정 시간 이상 마우스가 클릭된 상태로 계속 될 때)
- 롱클릭 판정 취소 (일정 시간 내에 마우스 클릭이 중단되었을 때)

를 각각 검출 할 수 있는 스트림을 **오퍼레이터 체인만** 쓰는 패턴과 **코루틴과 결합**하는 패턴 2종류를 만들어 보았습니다.

## 오퍼레이터 체인만 사용하는 경우

```cs
private void Start()
{
    var mouseDownStream = this.UpdateAsObservable()
        .Where(_ => Input.GetMouseButtonDown(0));
    var mouseUpStream = this.UpdateAsObservable()
        .Where(_ => Input.GetMouseButtonUp(0));

    // 롱클릭 판정
    mouseDownStream
        // 마우스 클릭되면 3초 후 OnNext를 흐르게 한다.
        .SelectMany(_ => Observable.Timer(TimeSpan.FromSeconds(3)))
        // 도중에 MouseUp되면 스트림을 재설정 한다.
        .TakeUntil(mouseUpStream)
        .RepeatUntilDestroy(gameObject)
        .Subscribe(_ => Debug.Log("롱클릭"));

    // 롱클릭 취소 판정
    mouseDownStream.Timestamp()
        .Zip(mouseUpStream.Timestamp(), (d, u) => (u.Timestamp - d.Timestamp).TotalMilliseconds / 1000.0f)
        .Where(time => time < 3.0f)
        .Subscribe(t => Debug.Log(t + "초에서 취소"));
}
```

동작을 파악 하는 것이 조금 복잡하지만, 일단은 작동 합니다.

[역주] 

아래 오퍼레이터의 동작을 알고 있어야 정확한 이해가 가능 합니다.

- SelectMany
    - 스트림 자체를 다른 스트림으로 대체해 버린다.
    - 위 예에서는 mouseDownStream 스트림 자체를 Observable.Timer로 대체 한다고 생각하면 된다. (마우스 클릭 스트림이 발행된 이후 3초 후에 대한 스트림을 새로 만듬)
- TakeUntil
    - 스트림의 메시지가 오면 OnCompleted를 통해 스트림을 종료시킨다.
    - 위 예에서는 mouseUpStream이 발행되면 OnCompleted가 발행되고, Repeat가 실행되면서 다시 Subscribe 하게 되는 구조 ([Repeat란?]({% post_url 2019-10-12-UniRx-Repeat %}))
- Timestamp
    - 스트림이 발행한 시간을 타임스탬프 값으로 반환한다.
- Zip
    - 두개의 오퍼레이터가 모두 실행될때에 대한 처리를 하나로 묶어 처리해준다.

## 코루틴과 조합하는 경우

```cs
public class LongClick : MonoBehaviour
{
    #region 롱클릭 스트림
    private Subject<Unit> _longClickSubject;

    private IObservable<Unit> LongClickAsObservable =>
        _longClickSubject ?? (_longClickSubject = new Subject<Unit>());
    #endregion

    #region 롱클릭 취소 스트림
    private Subject<float> _longClickCancelSubject;

    private IObservable<float> LongClickCancelAsObservable =>
        _longClickCancelSubject ?? (_longClickCancelSubject = new Subject<float>());
    #endregion

    private Coroutine _longClickCoroutine;

    private void Start()
    {
        _longClickCoroutine = StartCoroutine(LongClickCoroutine(3.0f, _longClickSubject, _longClickCancelSubject));

        LongClickAsObservable.Subscribe(_ => Debug.Log("롱클릭"));
        LongClickCancelAsObservable.Subscribe(t => Debug.Log(t + "초 취소"));
    }

    /// <summary>
    /// 롱클릭 판정 코루틴
    /// </summary>
    private IEnumerator LongClickCoroutine(float threshold,
        IObserver<Unit> longClickObserver,
        IObserver<float> longClickCancelObserver)
    {
        var isLongClicked = false;
        var count = 0.0f;

        while (true)
        {
            if (Input.GetMouseButton(0))
            {
                count += Time.deltaTime;
                if (!isLongClicked && count > threshold)
                {
                    isLongClicked = true;
                    longClickObserver.OnNext(Unit.Default);
                }
            }
            else if (Input.GetMouseButtonUp(0))
            {
                isLongClicked = false;
                if (count > 0)
                {
                    longClickCancelObserver.OnNext(count);
                    count = 0;
                }
            }
            yield return null;
        }
    }

    private void OnDestroy()
    {
        if (_longClickCoroutine != null)
            StopCoroutine(_longClickCoroutine);
    }
}
```

판정 처리를 무한 루프하는 코루틴안에 쓰고, 결과를 스트림으로 외부에 통지하는 형태로 구현하였습니다.

오퍼레이터 체인을 사용하여 흐름을 파악하기 힘든 스트림이 된다면, 포기하고 절차적으로 쓰는 것이 더 좋을 수 있습니다. 잡다한 처리는 전부 코루틴에 장착되어 사용하는 측에서는 알 수 없기 때문에 허용 범위 일수도 있을까 싶습니다.

또한 이러한 범용적으로 사용할 것 같은 처리는 ObservableTrigger로 정의해두면 좋을지도 모르겠습니다.

[역주]

`Observable.FromCoroutine` 을 사용하는 방안을 생각해 보았는데, 해당 메서드는 Observer을 1개 밖에 사용하지 못하는 방식이라, 코루틴 2개(판정, 취소)를 각각 만들어줘야 되는 방식입니다. 위 예처럼 2개 이상의 Observer을 사용하는 경우는 Subject를 만들고 해당 Subject의 OnNext를 호출하는 방식이 더 나은 방법이라고 판판이 되네요.