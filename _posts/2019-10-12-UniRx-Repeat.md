---
layout: post
title: "[R[ReactiveExtensions] "Repeat"이란 무엇인가? 번역"
date: 2019-10-12 10:47:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/59d10ddec2e89b86600c](https://qiita.com/toRisouP/items/59d10ddec2e89b86600c)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

Rx의 [Repeat](http://reactivex.io/documentation/operators/repeat.html)는 자주 사용하는 것에 비해, 초심자가 어떻게 동작 하는지 파악하기 힘든 오퍼레이터라고 생각합니다.

그래서 Repeat에 대해 조사해 정리해 보았습니다.

## Rx의 "Repeat"란?

즉 Repeat는 **"OnCompleted가 왔을 때 다시 Subscribe 해주는 오퍼레이터"** 입니다. (더 정확히 말하면, OnCompleted가 왔을 때 같은 스트림을 생성하고 [Concat](http://reactivex.io/documentation/ko/operators/concat.html)으로 뒤에 연결해주는 오퍼레이터 입니다.)

드물게 "흘러온 메시지를 재현하고 반복 해주는 오퍼레이터"라고 착각하는 사람이 있지만 그렇지 않습니다.

예를 들어 다음의 코드를 참조하십시오.
```cs
var random = new Random();

// 난수를 하나 반환 스트림
Observable.Create<int>(observer =>
{
    observer.OnNext(random.Next());
    observer.OnCompleted();
    return () => { };
})
.Repeat(3)
.Subscribe(x => Debug.WriteLine(x), () => Debug.WriteLine("OnCompleted"));
```

[역주] 아래는 UniRx 위 동작과 동일한 구현을 한 예시 입니다. (UniRx에서는 Repeat 오퍼레이터에 인자 값을 넣는 기능이 없네요.) 그리고 Repeat를 사용하면, 에러가 발생 (NullException, Stack OverFlow 등) 하여 RepeatUntilDestroy로 변경 하였습니다.

```cs
var random = new System.Random();
        
// 난수를 1개 반환하는 스트림
Observable.Create<int>(observer =>
    {
        observer.OnNext(random.Next());
        observer.OnCompleted();
        return Disposable.Empty;
    })
    .RepeatUntilDestroy(gameObject)
    .Take(3)
    .Subscribe(x => Debug.Log(x), () => Debug.Log("OnCompleted!"));
```

실행결과

    30200140
    1282005623
    1140074942
    OnCompleted!

3회 모두 다른 값이 OnNext에 흘러 왔습니다.

이것은 Repeat 타이밍에서 Observable.Create가 다시 실행되고 있기 때문 입니다.

이렇게 Repeat는 OnCompleted를 감지 한 순간에 다시 Subscribe하고 Concat으로 스트림 뒤에 새로운 스트림을 다시 연결 해 주는 오퍼레이터 입니다.

**Repeat는 값을 캐시하고 반복해서 같은 값을 흘리는 기능을 가지고 있지 않습니다.**

## Repeat의 용도

## 용도 1) 스트림을 반복 Subscribe하기

OnCompleted가 발행되었을 때 다시 한번 같은 스트림을 Subscribe하는 간단한 방법입니다.

마우스 드래그 모니터링을 반복

```cs
var mouseMove = this.UpdateAsObservable()
            .Select(_ => Input.mousePosition);
var mouseDown = this.OnMouseDownAsObservable();
var mouseUp = this.OnMouseUpAsObservable();

mouseMove
    .SkipUntil(mouseDown)
    .TakeUntil(mouseUp)
    .RepeatUntilDestroy(gameObject)
    .Subscribe(pos => Debug.Log(pos.x));
```

## 용도 2) 오퍼레이터 상태를 초기화 하기

아까도 설명했지만, Repeat는 OnCompleted가 왔을 때 Subscribe를 다시 하는 오퍼레이터 입니다.

이 **다시 SubScribe 해준다**는 것이 핵심입니다.

Rx 대부분의 오퍼레이터는 Subscribe시 생성되는 성질이 있습니다. 따라서 "다시 Subscribe 한다 = 오퍼레이터를 모두 초기화 한다"라고 생각할 수 있습니다.

5초 지나면 카운터를 다시 0으로 초기화
```cs
Observable.Timer(TimeSpan.FromSeconds(1), TimeSpan.FromSeconds(1))
    .Take(5)
    .Repeat()
    .Subscribe(time => Debug.Log(time));
```

클릭하면 타이머를 다시 0으로 초기화
```cs
var mouseClick = this.UpdateAsObservable()
            .Where(_ => Input.GetMouseButtonDown(0));

Observable.Timer(TimeSpan.FromSeconds(1), TimeSpan.FromSeconds(1))
    .TakeUntil(mouseClick)
    .Repeat()
    .Subscribe(time => Debug.Log(time));
```

재설정 이벤트가 오면 Buffer를 지운다
```cs
hogeStream
    .Buffer(10)
    .TakeUntil(resetStream)
    .Repeat()
    .Subscribe(data => Debug.WriteLine(data.Count));
```

TakeUntil + Repeat 또는 First + Repeat를 스트림 중간에 끼워 주는 것으로 언제라도 스트림을 초기화 할 수 있습니다.

## Repeat를 사용하는데 있어서 주의해야 할 점

**무한 Repeat는 상당히 위험하다는 것을 인식해야 합니다.**

무한 Repeat는 주의하지 않으면 프리징이나, stack overflow를 일으키게 됩니다.

## 위험한 예) 팩토리 메서드의 무한 Repeat

팩토리 메서드 중 Subscribe 직후에 OnNext/OnCompleted를 발행하는 것이 몇 개 있습니다. 이렇게 Subscribe 직후 OnCompleted를 반환하는 Observable의 경우 Repeat가 무한히 반복 하여 프리징이나 stack overflow가 발생하여 죽어 버립니다.

Return은 무한 Repeat는 위험하다.

```cs
Observable.Return(0)
          .Repeat()
          .Subscribe(x => Debug.WriteLine(x));
```

그러나 모든 팩토리 메서드가 위험하다는 것은 아닙니다.

예를 들어 Observable.Timer는 무한 Repeat 문제 없이 사용할 수 있습니다.

(그러나 Dispose를 빼먹을 수 있으니 주의가 필요 합니다.)

## 위험한 예 2) 스트림의 근원에서 OnCompleted가 발행되는 경우

Subscribe하고 있는 스트림의 근원에서 OnCompleted가 발행된 경우 또는 Hot 변환 스트림에서 OnCompleted가 발행된 경우 무한 Repeat가 발생합니다.

Subject의 무한 Repeat
```cs
var subject = new Subject<int>();
subject.Materialize().Repeat().Subscribe(x => Debug.WriteLine(x));

subject.OnNext(1);
subject.OnNext(2);
subject.OnNext(3);
subject.OnCompleted();
```

실행 결과

    OnNext(1)
    OnNext(2)
    OnNext(3)
    OnCompleted()
    OnCompleted()
    OnCompleted()
    OnCompleted()
    OnCompleted()
    .
    .
    (이하 무한 OnCompleted())

스트림의 근원이 OnCompleted가 되어 버리면 여러 번 Subscribe해도 즉시 OnCompleted가 반환 되어버립니다. 그 때문에 Repeat를 끼우고 있으면 무한히 Repeat를 시도 하고 죽게 됩니다.

따라서 Repeat를 사용하는 경우 **스트림의 근원에서 OnCompleted가 발행되는 이전에 Dispose를 호출하는 등의 스트림의 수명 관리를 제대로 할 필요가 있습니다.**

## 정리

**Repeat는 OnCompleted가 왔을 때 Subscribe를 다시 해주는 오퍼레이터이며, 값을 유지하고 반복해준다는 기능은 없는 오퍼레이터 입니다.**

또한 Repeat를 사용하는 경우 OnCompleted의 발행 타이밍에 주의를 기울일 필요가 있고, 방심하면 곧바로 프리징 또는 stack overflow를 발생 시킬 수 있습니다.

## 보충) Observable.Return

다음 코드와 실행 결과를 참조하십시오.
```cs
var random = new Random();

// 난수를 반환
Observable.Return(random.Next())
    .RepeatUntilDestroy(gameObject)
    .Take(3)
    .Subscribe(x => Debug.Log(x), () => Debug.Log("OnCompleted"));
```

실행 결과

    873345220
    873345220
    873345220
    OnCompleted

Observable.Return을 Repeat하면 Observable.Create 때와는 달리 항상 같은 값이 반복되어 버립니다.

이것은 **Observable.Return이 지연 평가가 아닌** 것이 원인이며, Repeat에 기인하는 것은 아닙니다.

만약 Observable.Return을 지연 평가 한 후 Repeat하려는 경우(정말 필요하다면??)는 Observable.Defer로 Observable.Return을 감싸면 됩니다.
```cs
var random = new Random();
Observable.Defer(() => Observable.Return(random.Next()))
    .RepeatUntilDestroy(gameObject)
    .Take(3)
    .Subscribe(x => Debug.Log(x), () => Debug.Log("OnCompleted"));
```

실행 결과

    1413311669
    1173910711
    1011315106
    OnCompleted

Defer로 포장하여 Repeat 다시 Subscribe시까지 평가를 지연시킬 수 있습니다.