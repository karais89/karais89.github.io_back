---
layout: post
title: "스트림의 메시지를 서로 비교하기"
excerpt: "스트림의 메시지를 서로 비교하기 번역"
date: 2019-10-20 23:22:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/f5cb995d811e6bcef293](https://qiita.com/toRisouP/items/f5cb995d811e6bcef293)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

UniRx보다는 Reactive Extensions의 내용 입니다.

UniRx에서 구현을 진행하는데 있어서 "어떤 스트림을 흐르는 메시지 끼리를 비교하고 싶다" 라는 것을 하고 싶을 수 있습니다. 직전의 프레임에서의 플레이어 좌표와 지금 프레임에서의 프레임에서의 플레이어의 좌표를 비교하고 싶은것과 같은, 이러한 처리는 **Buffer 오퍼레이터를** 사용하면 간단하게 구현할 수 있습니다.

그런 이유로 이번에는 Buffer의 Skip 과 그것을 사용한 구현 사례를 소개하고 싶습니다.

## Buffer의 오버로드

Buffer 오퍼레이터는 오버로드가 여러가지가 있는데 그 중 하나인 Skip 수를 지정할 수 있는 것이 있습니다. Skip을 지정하여 Buffer가 방류 한 뒤 어느 타이밍에 다시 방류할지를 지정 할 수 있습니다. Skip을 지정하지 않으면 **Buffer 지정한 Count와 같은 값이 Skip로 설정됩니다.**

```cs
Observable.Range(1, 10)
          .Select(x => x.ToString())
          .Buffer(2) // 2개 묶음 (방류한 뒤에 2개 건너뛰고 다음을 방류한다. Buffer(2,2)와 동일)
          .Subscribe(x =>
          {
              // buffer의 내용을 표시
              Debug.Log(x.Aggregate<string>((p, c) => p.ToString() + ", " + c.ToString()));
          });
```

실행 결과

    1,2
    3,4
    5,6
    7,8
    9,10

---

```cs
Observable.Range(1, 10)
          .Select(x => x.ToString())
          .Buffer(2, 1) // 2개 묶음. 방류 후 1개 건너뛰고 방류하다.
          .Subscribe(x =>
          {
          // buffer의 내용을 표시
          Debug.Log(x.Aggregate<string>((p, c) => p.ToString() + ", " + c.ToString()));
          });
```

실행 결과

    1,2
    2,3
    3,4
    4,5
    5,6
    6,7
    7,8
    9,10
    10

---

```cs
Observable.Range(1, 10)
          .Select(x => x.ToString())
          .Buffer(3, 2) // 3개 묶음. 방류 후 2개 건너뛰고 방류하다.
          .Subscribe(x =>
          {
              // buffer의 내용을 표시
              Debug.Log(x.Aggregate<string>((p, c) => p.ToString() + ", " + c.ToString()));
          });
```

실행 결과

    1,2,3
    3,4,5
    5,6,7
    7,8,9
    9,10

---

이 Buffer의 Skip을 이용하면 쉽게 스트림 메시지의 비교 및 연산을 할 수 있습니다.

## 예) Buffer를 사용하여 이전 메시지의 차이를 구하기

Buffer(2,1)을 사용하면 쉽게 구현할 수 있습니다. (Zip과 Skip(1)을 이용해서 구현하는 사람도 보았습니다. 하지만 Buffer가 솔직히 더 쉽게 구현할 수 있습니다.)

다만, OnCompleted가 발행되었을 때에 Buffer는 값이 갖추어지지 않아도 방출해 버리는 특성이 있기 때문에, Where에서 필요 수가 갖추어지지 않은 경우 cut하는 등의 처리를 넣어줄 필요가 있습니다.

transform.Postion의 차이를 구한다.

```cs
this.UpdateAsObservable()
    .Select(_ => transform.position)
    .Buffer(2, 1)
    .Where(x => x.Count == 2)
    .Select(x => x.Last() - x.First())
    .Subscribe(x => Debug.Log("Delta: " + x));
```

## 예) Buffer을 이용하여 과거 n개의 메시지 값의 평균값을 구한다.

Buffer (n, 1)에 LINQ의 Average를 조합하면 쉽게 얻을 수 있습니다.

지난 10프레임의 Time.deltaTime의 평균 값을 실시간으로 계산

```cs
this.UpdateAsObservable()
    .Select(_ => Time.deltaTime)
    .Buffer(10, 1)
    .Select(x => x.Average())
    .Subscribe(x => Debug.Log("Average : " + x));
```

응용: [FPS 카운터를 구현하자](https://qiita.com/toRisouP/items/1d0682e7a35cdb04bc38)

## 정리

Buffer는 단지 값을 막는 유일한 오퍼레이터가 아닙니다. 이와 같이 Skip을 지정할 수 있고, 시간을 구분해 정리하는 범용성이 높은 오퍼레이터이기도 합니다.

Buffer에 국한된 이야기가 아니라, 같은 오퍼레이터라도 오버로드(overload)로 전혀 행동이 달라지거나 하는 것들이 많이 있습니다. 무리하게 오퍼레이터 체인으로 로직을 실현하기 전에, 비슷한 오퍼레이터가 없는지 찾아 그 오버로드를 확인해 보면 좋을 것 같습니다.

## 추기

`Buffer(2,1)` 와 비슷한 행동을 하는 **Pairwise** 는 오퍼레이터도 있습니다.

OnCompleted가 발행되었을 때의 동작이 약간 다른것 이외에는 동일하기 때문에 이전 값만을 사용하고 싶으면 사용하는 것도 좋을지도 모릅니다.

```cs
// Pairwise()
Observable.Range(1, 10)
          .Pairwise()
          .Subscribe(x => Debug.Log($"{x.Previous}, {x.Current}"));

// Buffer (2,1)
Observable.Range(1, 10)
          .Select(x => x.ToString())
          .Buffer(2, 1)
          .Subscribe(x => Debug.Log(x.Aggregate<string>((p, c) => p.ToString() + ", " + c.ToString())));
```

실행 결과

    (Pairwise)
    1,2
    2,3
    3,4
    4,5
    5,6
    6,7
    7,8
    9,10
    
    (Buffer)
    1,2
    2,3
    3,4
    4,5
    5,6
    6,7
    7,8
    9,10
    10 // ←