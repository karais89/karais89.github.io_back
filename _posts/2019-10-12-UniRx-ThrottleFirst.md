---
layout: post
title: "[UniRx] 연속된 OnNext의 최초 값만 흘리는 오퍼레이터 ThrottleFirst"
excerpt: "[UniRx] 연속된 OnNext의 최초 값만 흘리는 오퍼레이터 ThrottleFirst 번역"
date: 2019-10-12 18:31:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/fe52e1582c14782af3ac](https://qiita.com/toRisouP/items/fe52e1582c14782af3ac)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

**UniRx 최신 버전부터 ThrottleFirst가 추가 되었습니다! ([UniRx 4.8.1 이상 버전](https://github.com/neuecc/UniRx/blob/4.8.1/Assets/UniRx/Scripts/Observable.Time.cs#L386-L389))**

[역주] [ThrottleFirstFrame](https://github.com/neuecc/UniRx/blob/4.8.2/Assets/UniRx/Scripts/UnityEngineBridge/Observable.Unity.cs#L556-L608)도 4.8.1 이상 버전에서 추가 되었습니다!

게임 개발에서 "어떤 처리를 하고 나서 한동안은 이벤트를 무시하고 싶다", "이벤트가 많이 왔을 때 처음만 처리하고 나머지는 당분간 무시하고 싶다"는 수요는 많을 것이라고 생각합니다.

(예를 들어 "버튼을 길게 눌러 이벤트를 300밀리 초 간격으로 솎아 내고 싶다", "마지막으로 애니메이션 이벤트가 오고 나서 3초 동안 이벤트를 무시하고 싶다" 등)

~~기존의 Rx의 오퍼레이터 조합으로도 구현 할 수 있지만, 자주 사용되는 만큼 전용 오퍼레이터를 가지고 싶었습니다. 그래서 Throttle를 바탕을 하여 제작 하였습니다.~~

**RxJS 등에는 "[ThrottleFirst](http://reactivex.io/documentation/operators/sample.html)"라는 이름으로 같은 동작을 하는 오퍼레이터가 존재하고 있었습니다.**

![](/images/unity3d/2019-10-12-1.png)

[이미지는 ReactiveX에서 인용](http://reactivex.io/)

## UniRx에서 ThrottleFirst

```cs
using System;
namespace UniRx
{
    public static partial class Observable
    {
        public static IObservable<TSource> ThrottleFirst<TSource>(this IObservable<TSource> source, TimeSpan dueTime)
        {
            return source.ThrottleFirst(dueTime, Scheduler.DefaultSchedulers.TimeBasedOperations);
        }

        public static IObservable<TSource> ThrottleFirst<TSource>(this IObservable<TSource> source, TimeSpan dueTime, IScheduler scheduler)
        {
            return new AnonymousObservable<TSource>(observer =>
            {
                var gate = new object();
                var open = true;
                var cancelable = new SerialDisposable();

                var subscription = source.Subscribe(x =>
                {
                    lock (gate)
                    {
                        if (!open) return;
                        observer.OnNext(x);
                        open = false;
                    }

                    var d = new SingleAssignmentDisposable();
                    cancelable.Disposable = d;
                    d.Disposable = scheduler.Schedule(dueTime, () =>
                    {
                        lock (gate)
                        {
                            open = true;
                        }
                    });

                },
                    exception =>
                    {
                        cancelable.Dispose();

                        lock (gate)
                        {
                            observer.OnError(exception);
                        }
                    },
                    () =>
                    {
                        cancelable.Dispose();

                        lock (gate)
                        {
                            observer.OnCompleted();

                        }
                    });

                return new CompositeDisposable(subscription, cancelable);
            });
        }

        public static IObservable<TSource> ThrottleFirstFrame<TSource>(this IObservable<TSource> source, int frameCount,
            FrameCountType frameCountType = FrameCountType.Update)
        {
            return new AnonymousObservable<TSource>(observer =>
            {
                var gate = new object();
                var open = true;
                var cancelable = new SerialDisposable();

                var subscription = source.Subscribe(x =>
                {
                    lock (gate)
                    {
                        if (!open) return;
                        observer.OnNext(x);
                        open = false;
                    }

                    var d = new SingleAssignmentDisposable();
                    cancelable.Disposable = d;

                    d.Disposable = Observable.TimerFrame(frameCount, frameCountType)
                        .Subscribe(_ =>
                        {
                            lock (gate)
                            {
                                open = true;
                            }
                        });
                },
                    exception =>
                    {
                        cancelable.Dispose();

                        lock (gate)
                        {
                            observer.OnError(exception);
                        }
                    },
                    () =>
                    {
                        cancelable.Dispose();

                        lock (gate)
                        {
                            observer.OnCompleted();

                        }
                    });

                return new CompositeDisposable(subscription, cancelable);
            });
        }
    }
}
```

- ThrottleFirst (OnNext를 무시하는 시간)
- ThrottleFirstFrame (OnNext를 무시하는 프레임 수)

둘 다 첫 번째는 반드시 OnNext를 통과 시킵니다.

2 번째 이후의 OnNext 내용은 마지막으로 OnNext를 통과시킨 후 일정 시간 경과 할 때까지 OnNext를 흘리지 않고 버리도록 처리하고 있습니다.

예를 들어 `ThrottleFirst(TimeSpan.FromSeconds(1))` 와 같은 지정 방법을 한 경우에는 메시지가 1초 이내에 연속 해 온 경우는 처음에만 통과하고 그 이외는 무시하게 됩니다.

[역주]

[AnonymousObservable](http://neue.cc/2010/07/05_265.html) 클래스에 대한 정체가 궁금하다. (C# Rx에서 기본적으로 제공해주는 클래스를 UniRx 상에서도 사용할 수 있게 구현된 클래스 인 것 같다. 익명 함수 개념과 비슷한 느낌이라고 생각하면 될 것 같다. Observable은 만들고 싶지만, 이름은 지어 주고 싶지 않은 경우 사용하는 용도)

UniRx 5.0 이상 버전 부터는 오퍼레이터를 생성하는 구조가 변함. 위 클래스를 사용하지 않고, 각각의 오퍼레이터에 해당되는 Observable 클래스를 따로 만드는 구조 (OperatorObservableBase를 상속 받고, 필요한 내용들을 구현 하는 방식) 개인적으로는 바뀐 구조가 더 깔끔한 느낌이 든다.

## 사용 사례

클릭 되고 나서 5초 동안 클릭을 무시하는 예제

```cs
this.UpdateAsObservable()
    .Where(_=>Input.GetMouseButtonDown(0))
    .ThrottleFirst(TimeSpan.FromSeconds(5))
    .Subscribe(x => Debug.Log("Clicked!"));
```

업데이트를 1/10로 솎아 내는 예제 (9회 Update가 올 때까지 무시)
```cs
this.UpdateAsObservable()
    .ThrottleFirstFrame(9)
    .Subscribe(x => Debug.Log("tenth part Update"));
```