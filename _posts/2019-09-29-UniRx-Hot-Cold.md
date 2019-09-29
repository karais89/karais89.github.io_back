---
layout: post
title: "Rx의 Hot과 Cold 대해"
excerpt: "Rx의 Hot과 Cold 대해 번역"
date: 2019-09-29 21:35:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/f6088963037bfda658d3](https://qiita.com/toRisouP/items/f6088963037bfda658d3)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

Rx의 IObservable<T>는 Hot/Cold라는 큰 두가지 특징이 있습니다.

이러한 성질을 이해하지 않은 채 스트림을 설계하면 의도된 동작을 해주지 않는 경우가 있습니다.

이번에는 이 Hot/Cold의 성격에 대해 간략하게 정리하고자 합니다.

<br />

## 요약

<br />

## 한마디로 말하면?

- Cold: 스트림의 전후를 연결하는 파이프. 단독으로는 의미가 없다. 대부분의 오퍼레이터는 Cold이다.
- Hot: 스트림에서 값을 계속 발행하는 수도꼭지. 항상 흐린다. 뒤에 파이프를 많이 연결 할 수 있다.

<br />

## 자세히 설명하면

**Cold Observable**

- 자발적으로 아무것도 하지 않는 수동적인 Observable
- Observer이 등록되어 (Subscribe 되고) 처음 일을 시작한다.
- 스트림의 전후를 그냥 연결만 한다. 스트림을 분기시키는 기능은 없다.

**Hot Observable**

- 자신이 값을 발행하는 능동적인 Observable
- 후속 Observer의 존재에 관계없이 메시지를 발행한다
- 자신보다 상류의 Cold Observable을 시작하고 값의 발행을 요구하는 기능을 가진다
- 하류의 Observer를 모두 묶어, 정리해 같은 값을 발행한다 (스트림을 분기시킨다)

<br />

## Hot과 Cold 구분법

**대부분의 오퍼레이터는 Cold인 성질이며, 자신이 명시적으로 스트림(stream)을 Hot으로 변환하지 않는 한 Cold 그대로 입니다.**

Hot 변환용 오퍼레이터는 이른바 Publish 계의 오퍼레이터가 해당 합니다.

<br />

## Hot에 대해

<br />

## Hot Observable의 성질

<br />

### 스트림을 가동시키는 성질

Rx 스트림은 기본적으로 Subscribe가 된 순간에 각 오퍼레이터의 작동이 시작하게 되어 있습니다. 하지만 Hot Observable을 스트림 중간에 끼우는 것으로, Subscribe를 샐행 이전에 스트림을 실행시킬 수 있습니다.

<br />

### 스트림을 분기하는 성질

Hot Observable은 **스트림을 분기 할** 수 있습니다.

![](/images/unity3d/2019-09-29-1.jpeg)

## Cold에 대해

<br />

## Subscribe 될 때까지 작동하지 않는 성질

Cold Observable은 Subscribe될때 (또는 Hot 변환될때)까지 작동하지 않습니다. ~~마음이 없는~~ Observable 입니다.

**작동하지 않는** Cold Observable에 전달된 메시지는 모두 처리 조차 되지 않고 소멸 됩니다.

![](/images/unity3d/2019-09-29-2.jpeg)

특히 값의 발행 타이밍이나 전후 관계가 중요한 오퍼레이터를 사용하는 경우는, 어느 타이밍부터 처리가 시작되는지를 충분히 인지하고 사용하지 않으면 안됩니다. 같은 스트림 정의라도, Subscribe 한 타이밍에 따라서 동작이 바뀌어 버립니다. 아래가 그 예 입니다.

ColdExample.cs
```csharp
var subject = new Subject<string>();
        
// subject에서 생성된 Observable은 [Hot]
var sourceObservable = subject.AsObservable();

// 스트림에 흘러 들어온 문자열을 연결하여 새로운 문자열로 만드는 스트림
// Scan()은 [Cold]
var stringObservable = sourceObservable.Scan((p, c) => p + c);

// 스트림에 값을 흘린다
subject.OnNext("A");
subject.OnNext("B");

// 스트림에 값을 흘린 후 Subscribe 한다.
stringObservable.Subscribe(Debug.Log);

// Subscribe 후 스트림에 값을 흘린다.
subject.OnNext("C");

// 완료
subject.OnCompleted();
```

실행결과

    C

위의 코드를 실행한 결과 `C`가 출력 될 것입니다.

이것은 `Scan` 오퍼레이터가 Cold이기 때문에 Subscribe 전에 발행된 `A` 그리고 `B` 가 처리되지 않았기 때문입니다.

만약 여기에서 "Subscribe하기 이전에 발급 된 값을 처리 했으면 좋겠다"는 경우는 어떻게 하면 좋을까요. 이 경우 Hot 변환 오퍼레이터를 끼워 Subscribe하기 이전에 스트림을 시작하면 좋을 것입니다.

```csharp
var subject = new Subject<string>();
        
// subject에서 생성된 Observable은 [Hot]
var sourceObservable = subject.AsObservable();

// 스트림에 흘러 들어온 문자열을 연결하여 새로운 문자열로 만드는 스트림
// Scan()은 [Cold]
var stringObservable = sourceObservable
    .Scan((p, c) => p + c)
    .Publish(); // Hot 변환 오퍼레이터

stringObservable.Connect(); // 스트림 가동 개시

// 스트림에 값을 흘린다
subject.OnNext("A");
subject.OnNext("B");

// 스트림에 값을 흘린 후 Subscribe 한다.
stringObservable.Subscribe(Debug.Log);

// Subscribe 후 스트림에 값을 흘린다.
subject.OnNext("C");

// 완료
subject.OnCompleted();
```

실행 결과

    ABC

`Publish` 라는 Hot 변환 연산자를 사이에 끼우는 것으로, Subscribe하는 이전에 스트림을 강제로 실행시킬 수 있습니다.

<br />

## 각각의 Observer에 대해 별도의 처리를 한다 (스트림의 분기점이 되지 않는다.)

Cold Observable은 스트림을 분기시키는 성질을 가지고 있지 않습니다.

따라서 Cold Observable을 여러 Subscribe하는 경우 각각 **별도의 스트림이 생성되고 할당 될** 것입니다.

![](/images/unity3d/2019-09-29-3.jpeg)

---

그러나 스트림에 Hot Observable이 존재하는 경우 가장 말단에 가까운 Hot Observable로 스트림이 분기되어, 또 다른 별도의 스트림이 생성됩니다.

![](/images/unity3d/2019-09-29-4.jpeg)

<br />

## 정리

**스트림이 어디에서 분기 하는가?** 항상 의식하고 설계하는 것이 중요합니다.

"클래스 외에 Observable을 공개 할 때는 Cold인 채로 공개하지 말고, 반드시 말단에서 Hot으로 변환한 후 공개한다"등 의도하지 않은 곳에서 스트림이 분기되어 버리지 않도록 확실하게 제어해 줄 필요가 있습니다.

또한 Cold → Hot 변환에는 적용 오퍼레이터가 준비되어 있으므로, 그 쪽을 이용하면 좋을 것 같습니다.

(Publish, PublishLast, Multicast 등)

자세히 : [[Reactive Extensions] Hot 변환은 어떤 때에 필요한가?](https://qiita.com/toRisouP/items/c955e36610134c05c860)