---
layout: post
title: "UniRx 오퍼레이터 소개"
excerpt: "UniRx 오퍼레이터 소개 번역"
date: 2019-10-31 09:16:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/3cf1c9be3c37e7609a2f](https://qiita.com/toRisouP/items/3cf1c9be3c37e7609a2f)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

UniRx에서 "XX를 하고 싶지만 효율적인 방법을 모르겠어요!" 라고 하는 분들을 위해 UniRx에서 할 수 있는 것을 역으로 정리해 보았습니다.

## 전제

- Observable을 Subscribe (또는 Connect) 된 시점에서 생성된다.
- Observable을 흐르는 메시지는 OnNext, OnError, OnCompleted의 3 종류가 있다.
- "Observable의 마지막 값"은 "OnCompleted 발행시에 마지막으로 발행 된 OnNext"라는 의미

## 오퍼레이터 목록

*가 붙은 것은 복수의 용도가 있거나 표현이 다른 오퍼레이터




## 팩토리 메서드

| ﻿하고 싶은 일 | 오퍼레이터 | 비고 |
|-------------------------------------------------------------|------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| 값을 하나만 발행하고자 할때 | Observable.Return |  |
| 값을 반복 실행하고자 할때 | Observable.Repeat |  |
| 지정한 범위에서 수치를 발행하고자 할때 | Observable.Range |  |
| Observable 정의를 Subscribe때까지 지연시키고 싶을때 | Observable.Defer |  |
| 일정 시간 후에 값을 표시하려고자 할때 | Observable.Timer * |  |
| 지정 프레임 후에 값을 표시 하고자 할때 | Observable.TimerFrame * |  |
| 일정한 간격으로 값을 표시 하고자 할때 | Observable.Timer * / Observable.Interval * | Timer와 Interval의 차이는 타이머의 시작 타이밍 |
| 일정한 프레임 간격으로 값을 표시하고자 할때 | Observable.TimerFrame * / Observable.IntervalFrame * | Timer와 Interval의 차이는 타이머의 시작 타이밍 |
| 값을 발행하는 Observable을 스스로 원하는대로 만들고 싶을 때 | Observable.Create |  |
| OnError를 즉시 발행하고자 할 때 | Observable.Throw |  |
| OnCompleted를 즉시 발행하고자 할 때 | Observable.Empty |  |
| 아무것도 일어나지 않는 Observable을 정의하고자 할 때 | Observable.Never |  |
| C# Event를 Observable로 변환하고자 할 때 | Observable.FromEvent * / Observable.FromEventPattern |  |
| UnityEvent를 Observable로 변환하고자 할 때 | Observable.FromEvent * |  |
| Update를 Observable로 변환하고자 할 때 | Observable.EveryUpdate * | 실제로는 MainThreadDispatcher에서 코루틴이 실행됨 / OnCompleted는 발행되지 않으므로 수명 관리에 주의 / 평상시라면 UpdateAsObservable이 더 낫다 |
| FixedUpdate를 Observable로 변환하고자 할 때 | Observable.FixedEveryUpdate * | 실제로는 MainThreadDispatcher에서 코루틴이 실행됨 / OnCompleted는 발행되지 않으므로 수명 관리에 주의 / 평상시라면 FixedUpdateAsObservable이 더 낫다 |
|  |  |  |

## 메시지 필터

| ﻿하고 싶은 일 | 오퍼레이터 | 비고 |
|--------------------------------------------------------------------|----------------------------------------|----------------------------------------|
| 조건을 충족 시키는 것만 통과 시키고 싶다 | Where | 다른 언어에서는 "filter""이라 불린다." |
| 중복된 것을 제외하고 싶다 | Distinct |  |
| 값이 변했을 때만 통과 시키고 싶다 | DistinctUntilChanged |  |
| 함께 흘러온 OnNext의 마지막만 통과 시키고 싶다 | Throttle * / ThrottleFrame * |  |
| 함께 흘러온 OnNext의 첫번째만 통과 시키고 싶다 | ThrottleFirst * / ThrottleFirstFrame * |  |
| 가장 먼저 도달한 OnNext만 통과시키고 Observable을 완료 시키고 싶다 | First / FirstOrDefault |  |
| OnNext가 2개 이상 발행되면 오류를 발생시키고 싶다 | Single / SingleOrDefault |  |
| Observable의 마지막 값만 통과시키고 싶다 | Last/LastOrDefault |  |
| 처음부터 지정한 개수만 통과 시키고 싶다 | Take |  |
| 처음부터 조건이 성립되지 않을 때까지 통과 시키고 싶다 | TakeWhile |  |
| 처음부터 지정한 Observable에 OnNext가 올때 까지 통과 시키고 싶다 | TakeUntil |  |
| 처음부터 지정한 개수만큼 무시하고 싶다 | Skip |  |
| 처음부터 조건이 성립되는 동안 무시하고 싶다 | SkipWhile |  |
| 처음부터 지정한 Observable에 OnNext가 올때 까지 무시하고 싶다 | SkipUntil |  |
| 형태가 일치하는 것만 통과하고 싶다 (형변환도 동시에 하고 싶다) | OfType<T> |  |
| OnError 또는 OnCompleted만을 통과시키고 싶다 | IgnoreElements |  |

## Observable 자체의 합성

|하고 싶은 일| 오퍼레이터 | 비고 |
|-----------|-----------|-----------|
|여러 개의 Observable 중 가장 먼저 메시지가 온 Observable을 채택하고 싶다.| Amb | |
|복수의 Observable에 각각 1개씩 메시지가 오면 그것들을 합성하고 흐르게 하고 싶다. | Zip | |
|복수의 Observable에 각각 1개 이상 메시지가 오면 그것들을 합성하고 흐르게 하고 싶다(각각의 Observable의 최신의 메시지만 보유) | ZipLatest | |
|여러 개의 Observable 중 어느 하나에 값이 오면 다른 Observable의 과거 값과 합성해서 흘려보내고 싶다. | CombineLatest | |
|2개의 Observable중 한쪽을 주축으로 삼고 한쪽 Observable의 최신 값을 합성하고 싶어 | WithLatestFrom | |
|복수의 Observable을 1개에 정리하고 싶다 | Merge | |
|Observable의 OnCompleted 시 다른 Observable을 연결하고 싶다. | Concat | |
|Observable 값을 사용하여 별도의 Observable을 만들고 각각의 값을 합성하고자 한다. | SelectMany* | 다른 언어에서는 "flat Map" 이라 불린다. |
|여러 개의 Observable을 성공할 때까지 차례로 실행하고 싶다. | Observable.Catch | Catch(IEnumerable<IObservable<T>>)를 사용하면 차례로 성공할 때까지 1개씩 시행한다
 |



## Observable 자체 변환

| ﻿하고 싶은 일 | 오퍼레이터 | 비고 |
|-----------------------------------------------------------|----------------------------|-----------------------------------------|
| Observable을 Reactive Property로 변환하고 싶다. | ToReactiveProperty* |  |
| Observable을 Read Only Reactive Property로 변환하고 싶다. | ToReadOnlyReactiveProperty |  |
| 코루틴에서 Observable을 기다리고 싶다. | ToYieldInstruction | OnCompleted가 올 때까지 코루틴에서 대기 |

## Observable 분기

| ﻿하고 싶은 일 | 오퍼레이터 | 비고 |
|-------------------------------------------------------------------------|----------------------------|----------------------------------------------------------------------|
| Observable을 분기하고 싶다. | Publish/ToReactivePropety* | Publish 반환값은 IConnectabale Observable.Multicast(Subject)와 동의. |
| Observable을 분기하고, 초기값을 지정하고자 한다. | Publish | 인수를 주면 Multicast(Behavior Subject)와 동의하게 된다. |
| Observable을 분기하고, 그 때 Observable의 마지막 값만을 캐시 하고 싶다. | PublishLast | Multicast(Async Subject)와 동일 |
| Observable을 분기하고, 그 때에 지금까지 발행된 모든 OnNext를 받고 싶다. | Replay | Multicast(Replay Subject)와 동의. |
| Observable을 분기할때 Subject를 지정하고자 한다. | Multicast |  |
| Observer가 1가지라도 있으면 Connect하고 없으면 Dispose 한다 | RefCount | Publish()RefCount()는 거의 일반적으로 사용한다. |
| Publish().RefCount()를 축약하고 싶다. | Share |  |

## 메시지끼리의 합성 연산

| ﻿하고 싶은 일 | 오퍼레이터 | 비고 |  |
|------------------------------------------------------------------|------------|-----------------------------------------------|----------------|
| 메시지의 값과 이전 결과를 모두 사용 함수를 적용 할 | Scan | LINQ에서 말하는 Aggregate |  |
| 메시지를 일정 개수마다 정리하고 싶다 | Buffer * | 2번째 인수를 지정함으로써 동작이 바뀐다→상세. |  |
| 어떤 Observable에 메시지가 올 때까지 값을 막아 정리해 두고 싶다. | Buffer* | 인수에 Observable을 건네준다. |  |
| 직전의 메세지와 세트로 하고 싶다. | PairWise | "Bufer(2 | 1)와 비슷하다" |

## 메시지 변환

| 하고 싶은 일 | 오퍼레이터 | 비고 |
|-------------|----------|----------|
|값을 변화 하고 싶다 / 값에 함수를 적용 하고싶다 | Select | 다른 언어에서 "map" 이라 불린다 |
|형식 변환을 하고 싶다 | Cast <T> | |
|메시지의 값을 바탕으로 다른 Observable를 호출 그쪽 결과를 이용하고 싶다 | SelectMany * | Observable 합성 |
|메시지 이벤트의 메타 정보를 부여 하고싶다 | Materialize | OnNext / OnError / OnCompleted의 어느 것인가를 나타내는 정보를 부여한다 |
|마지막 메시지 이후의 시간을 부여 하고싶다 | TimeInterval | |
|메시지에 타임 스탬프를 부여하고 싶다 | TimeStamp | |
|메시지를 Unit 형식으로 변환하고 싶다 | AsUnitObservable | Select(_=>Unit.Default) 동일 |


## 시간에 얽힌 처리

| ﻿하고 싶은 일 | 오퍼레이터 | 비고 |
|------------------------------------------------------------------------------------|-----------------------------------------------------|------------------------------------------------|
| 일정 시간 후에 값을 표시하고싶다. | Observable.Timer * / Observabe.TimerFrame * | 인수를 하나만 지정하면 OneShot된다 |
| 일정한 간격으로 값을 표시하고싶다. | Observable.Timer * / Observable.Interval * | Timer과 Interval의 차이는 타이머의 시작 타이밍 |
| 일정한 프레임 간격으로 값을 표시하고싶다. | Observabe.TimerFrame * / Observable.IntervalFrame * | Timer과 Interval의 차이는 타이머의 시작 타이밍 |
| 메시지를 시간 지연시키고 싶다 | Delay / DelayFrame |  |
| Subscribe 후 일정 시간 이내에 OnNext이 오지 않는 경우 OnError를 발행하고싶다. | Timeout |  |
| 일정시간 이내에 모아서 값이 오면 안정될 때까지 기다렸다가 마지막 값을 흘리고 싶다. | Throttle * / ThrottleFrame * |  |
| 값이 오면 일정 시간 Observable을 차단하고 싶다. | ThrottleFirst * / ThrottleFirstFrame * |  |
| 일정한 간격으로 값을 꺼내고 싶다. | Sample |  |
| 다음 프레임으로 처리하고 싶다. | Observable.NextFrame |  |

## 비동기 처리

| ﻿하고 싶은 일 | 오퍼레이터 | 비고 |
|-----------------------------------------------------------------------|---------------------------------------|-----------------------------------------------------|
| 처리를 다른 스레드에서 수행 하고 싶다 | Observable.ToAsync / Observable.Start | ToAsync를 사용한 경우 Invoke를 호출하여 처리가 시작 |
| Observable의 메시지 처리 스레드를 전환하고싶다 | ObserveOn |  |
| Observable의 메시지 처리 스레드를 Unity의 메인 스레드로 전환하고 싶다 | ObserveOnMainThread | 다른 스레드에서 Unity 처리를 호출 할 때 필수 |
| Observable 구축의 실행 스레드를 전환하고 싶다 | SubscribeOn |  |
| Observable 결과를 코루틴에서 기다리고 싶다 | ToYieldInstruction | 편리해서 기억하고 싶은 |
| 비동기 처리를 연쇄시키고 싶다 | ContinueWith | SelectMany의 단발 판 |
ObserveOn과 SubscribeOn이 까다롭기에 주의가 필요합니다.

Subscribe On은 "**Subscribe 한 순간의 Observable을 구축하는 처리**를 어느 스레드 상에서 실행할 것인가"를 지정하는 오퍼레이터입니다.

`Subscribe(x=> /*여기 처리*/)` 의 실행 스레드를 지정하고자 하는 경우에 사용해야할 오퍼레이터는 **Observe On**쪽입니다.

## 오류 처리

| ﻿하고 싶은 일 | 오퍼레이터 | 비고 |
|-----------------------------------------------------------------------------------|--------------|------|
| OnError이 오면 다시 Subscribe하고 싶다 | Retry |  |
| OnError를 수신 오류 처리가 하고 싶다 | Catch |  |
| OnError를 수신 오류 처리를 한 후 OnError를 묵살하고 OnCompleted으로 대체하고 싶다 | CatchIgnore |  |
| OnError이 오면 오류 처리를 한 후 일정 시간 후 Subscribe 다시 원한다 | OnErrorRetry |  |

## Observable 완료시의 처리

| ﻿하고 싶은 일 | 오퍼레이터 | 비고 |
|-----------------------------------------------------------------------------------------------------------|--------------------|-------------------------------------------------------------------|
| OnCompleted가 오면 다시 Subscribe하고 싶다 | Repeat | 조심하지 않으면 무한 루프 발생 |
| OnCompleted가 오면 다시 Subscribe하고자하지만 단기간에 Subscribe이 반복되었을 때는 Repeat를 취소하고 싶다 | RepeatSafe | 무한 루프 방지판. 단지 Uncontrollable이기 때문에 추천하지 않는다. |
| OnCompleted가 오면 다시 Subscribe하고자하지만 지정된 GameObject가 Disable되면 Repeat를 취소하고싶다. | RepeatUntilDisable | Repeat보다 안전 |
| OnCompleted가 오면 다시 Subscribe하고자하지만 지정된 GameObject가 파기되면 Repeat를 취소하고싶다. | RepeatUntilDestory | Repeat보다 안전 |
| OnCompleted 또는 OnError가 왔을 때 처리를하고 싶다 | Finally |  |

## 기타

| ﻿하고 싶은 일 | 오퍼레이터 | 비고 |
|-------------------------------------------------------------|---------------|--------------------------------------------|
| Subscribe시 초기 값을 흐르고 싶다 | StartWith |  |
| 메시지의 결과를 T[]변환하려는 | ToArray | OnCompleted가 오지 않으면 발동하지 않는다. |
| Observable 결과를 동기화 기다리고 싶다 | Wait |  |
| Observable의 중간 결과를 로그에 내고 싶다 | Do |  |
| Observable 도중에 부작용을 일으키고 싶은 | Do |  |
| Subscribe 된 때 처리를하고 싶다 | DoOnSubscribe |  |
| 이 자리에서 메시지를 사용하여 후속으로는 Unit을 흘리고 싶다 | ForEachAsync | Last + Do + AsUnitObservable에 가까운 |
| 메시지 처리에 배타 락을 걸고 싶을 | Synchronize | lock하기위한 개체를 지정할 수도있다 |

## 오퍼레이터 이외

## Subject 계

| ﻿하고 싶은 일 | Subject |
|---------------------------------------------------------------------------------|------------------|
| 절차적으로 Observable을 구축하여 값을 발행하고자 한다. | Subject |
| Subject에 초기값을 갖게 하고 싶다 / Subscribe 시에 직전의 값을 발행하고자 한다. | BehaviorSubject |
| Subject에 과거에 발행 한 모든 값을 캐시하여 Subscribe시 함께 발행하고 싶다 | ReplaySubject |
| Subject를 비동기 처리에 사용하고 / 마지막 OnNext 하나만 캐시시켜 발행하고 싶다 | AsyncSubject |
| 변수에 대해서 Subscribe 하고 싶다. | ReactiveProperty |