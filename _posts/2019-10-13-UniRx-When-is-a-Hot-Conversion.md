---
layout: post
title: "[Reactive Extensions] Hot 변환은 어떤 때에 필요한가?"
excerpt: "[Reactive Extensions] Hot 변환은 어떤 때에 필요한가? 번역"
date: 2019-10-13 19:01:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/c955e36610134c05c860](https://qiita.com/toRisouP/items/c955e36610134c05c860)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

이전 [Rx의 Hot과 Cold 내용](https://qiita.com/toRisouP/items/f6088963037bfda658d3)으로 Observable의 Cold와 Hot 성질에 대해 설명했습니다.

이번에는 보다 구체적으로, 어떤 상황에서 Hot 변환을 하는지를 설명하고 싶습니다.

## Hot 변환 한 포인트

여러 상황이 있지만 가장 Hot 변환이 중요해지는 상황은 **하나의 스트림을 여러 번 Subscribe하는 경우** 입니다. 실제 코드를 보면서 설명 하겠습니다.

## 예) 입력된 문자열이 특정 키워드와 일치하는지 검사

Hot 변환이 필요한 예로 "입력된 키 입력을 감지하고 4 문자의 특정 키워드가 입력되었는지를 알아 내는 스트림"을 만들어 보겠습니다.

### 준비

우선은 준비 단계로 입력된 키 정보를 4글자씩 내놓는 스트림을 만듭니다.

```cs
var keyBufferStream
    = Observable.FromEvent<KeyEventHandler, KeyEventArgs>(
        h => (sender, e) => h(e),
        h => KeyDown += h,
        h => KeyDown -= h)
        .Select(x => x.Key.ToString()) // 입력 키를 문자로 변환
        .Buffer(4, 1) // 4 개씩 정리
        .Select(x => x.Aggregate((p, c) => p + c)); // 문자에서 문자열로 변환

// 결과를 표시하고 보니
keyBufferStream.Subscribe(Console.WriteLine);
```

실행결과 예(ABCDEFGH 키 입력 결과)

    ABCD
    BCDE
    CDEF
    DEFG
    EFGH

이같이 keyBufferStream은 입력 키가 4자씩으로 뭉치고 흐르는 스트림입니다.

[역주] 아래는 유니티에서 실행 가능한 예제 입니다. 
```cs
var keyBufferStream = this.UpdateAsObservable()
    .Where(_ => Input.anyKeyDown) // 아무 버튼 눌렀을 때
    .Where(_ => !(Input.GetMouseButtonDown(0) || Input.GetMouseButtonDown(1) || Input.GetMouseButtonDown(2))) // 마우스는 무시
    .Select(_ => Input.inputString) // 버튼 스트링
    .Buffer(4, 1) // 4 개씩 정리 
    .Select(x => x.Aggregate((p, c) => p + c)); // 문자에서 문자열로 변환
    
// 결과 표시
keyBufferStream.Subscribe(Debug.Log);
```

[Aggregate](https://docs.microsoft.com/ko-kr/dotnet/framework/data/adonet/method-based-query-syntax-examples-aggregate-operators)는 Linq 에서 지원 하는 메서드이며, 집계 연산자 입니다. 마지막 값을 돌려주는 메소드 입니다.

### keyBufferStream을 사용하여 "HOGE" 또는 "FUGA"의 입력을 감시하자

그럼 이 keyBufferStream을 사용하여 "HOGE"와 "FUGA"를 감시해 봅시다.

Where 사이 HOGE와 FUGA에서 2회 Subscribe 합니다.
```cs
keyBufferStream.Where(x => x == "HOGE")
    .Subscribe(_ => Debug.Log("Input HOGE"));

keyBufferStream.Where(x => x == "FUGA")
    .Subscribe(_ => Debug.Log("Input FUGA"));
```

실행 결과 (HOGEFUGA 입력 한 결과)

    Input HOGE
    Input FUGA

![](/images/unity3d/2019-10-13-1.jpeg)

각각의 문자열에 반응하는 스트림을 만들어 Subscribe 할 수 있었습니다.

만.. **이 스트림에는 커다란 문제가 있습니다.**

## 무엇이 문제인가?

---

상기 스트림은 무엇이 문제인가? 그것은 **keyBufferStream이 Cold Observable로 형성되는 것이 문제** 입니다. 이전의 포스트에서도 설명했지만, [Cold Observable은 분기하지 않습니다.](https://qiita.com/toRisouP/items/f6088963037bfda658d3#%E3%81%9D%E3%82%8C%E3%81%9E%E3%82%8C%E3%81%AEobserver%E3%81%AB%E5%AF%BE%E3%81%97%E3%81%A6%E5%88%A5%E3%80%85%E3%81%AE%E5%87%A6%E7%90%86%E3%82%92%E3%81%99%E3%82%8B%E3%82%B9%E3%83%88%E3%83%AA%E3%83%BC%E3%83%A0%E3%81%AE%E5%88%86%E5%B2%90%E7%82%B9%E3%81%AB%E3%81%AA%E3%82%89%E3%81%AA%E3%81%84) Subscribe 할 때마다 매번 새로운 스트림을 생성하는 특성이 있습니다.

따라서 상기와 같은 작성을 해 버리면 다음과 같은 문제가 발생할 수 있습니다.

- 뒤에서 다중 스트림이 생성되어 버립니다. 메모리와 CPU를 낭비합니다.
- Subscribe 한 시점에서 따라 흘러 나오는 결과가 다릅니다. ([참고 Cold Observable의 성질](https://qiita.com/toRisouP/items/f6088963037bfda658d3#subscribeされるまで動作しない))
    - [역주] Cold Observable은 Subscribe 한 순간부터 오퍼레이터가 작동하게 됩니다. Subscribe 전에 온 메시지는 모든 처리 조차 되지 않고 소멸 됩니다.

스트림이 2개로 흐른다는 증거
```cs
var keyBufferStream
    = Observable.FromEvent<KeyEventHandler, KeyEventArgs>(
        h => (sender, e) => h(e),
        h => KeyDown += h,
        h => KeyDown -= h)
        .Select(x => x.Key.ToString())
        .Buffer(4, 1)
        .Do(_=> Console.WriteLine("Buffered")) // Buffer가 OnNext를 방출한 타이밍에 출력된다.
        .Select(x => x.Aggregate((p, c) => p + c));

keyBufferStream
    .Where(x => x == "HOGE")
    .Subscribe(_ => Console.WriteLine("Input HOGE"));

keyBufferStream
    .Where(x => x == "FUGA")
    .Subscribe(_ => Console.WriteLine("Input FUGA"));
```

실행 결과(AAAA와 Buffer가 1번만 움직이도록 키 입력)

    Buffered
    Buffered // Buffer는 1회만 흐르고 있을텐데 2번 출력되고 있다 = 스트림이 2개로 흐르고 있다.

![](/images/unity3d/2019-10-13-2.jpeg)

Hot Observable이 스트림의 근원인 FromEvent 밖에 없기 때문에, Subscribe 할 때마다 FromEvent로부터 새롭게 스트림이 생성되어 버리는 움직임이 되고 있습니다.

## 문제의 해결책 "Hot 변환"

여기에서 첫번째 "Hot 변환은 **하나의 스트림을 동시에 여러 Subscribe하는 경우**에 사용한다" 라는 이야기로 돌아갑니다.

즉 Hot 변환하여 스트림의 분기점을 만들어 여러 Subscribe 했을 때 스트림을 하나로 통합 할 수 있게 되는 것입니다.

![](/images/unity3d/2019-10-13-3.jpeg)

Hot 변환 된 예
```cs
var keyBufferStream
    = Observable.FromEvent<KeyEventHandler, KeyEventArgs>(
        h => (sender, e) => h(e),
        h => KeyDown += h,
        h => KeyDown -= h)
        .Select(x => x.Key.ToString())
        .Buffer(4, 1)
        .Select(x => x.Aggregate((p, c) => p + c))
        .Publish() // Publish에서 Hot 변환(Publish가 대표하여 Subscribe 해 준다)
        .RefCount(); // RefCount은 Observer가 추가되었을 때 자동 Connect 해 주는 오퍼레이터.

keyBufferStream
    .Where(x => x == "HOGE")
    .Subscribe(_ => Console.WriteLine("Input HOGE"));

keyBufferStream
    .Where(x => x == "FUGA")
    .Subscribe(_ => Console.WriteLine("Input FUGA"));
```
실행 결과(HOGEFUGA 입력)

    Input HOGE
    Input FUGA

Hot 변환 방식에는 여러 가지가 있지만 가장 쉬운 것이 Publish()와 RefCount()를 결합 하는 것 입니다.

이번에는 Hot 변환의 필요성에 대해 설명하고 싶기 때문에 Publish와 RefCount의 상세한 설명은 생략하겠습니다.([자세한 설명은 여기](http://introtorx.com/Content/v1.0.10621.0/14_HotAndColdObservables.html#HotAndCold))

[역주] UniRx에서는 Publish()와 RefCount()의 결합인 [Share()](https://github.com/neuecc/UniRx/blob/7.1.0/Assets/Plugins/UniRx/Scripts/Observable.Binding.cs#L71-L74) 오퍼레이터를 제공 합니다. 두개를 사용해야 될 경우에는 Share()를 사용하시면 됩니다.

## 정리

- 스트림을 의도적으로 분기 하고 싶을 때 Hot 변환을 수행 한다.
- 스트림을 생성하여 반환하는 속성과 함수를 정의하면 끝에 Hot 변환을 하는 것이 안전하다.
- Hot 변환을 잊어 버리면 메모리나 CPU가 낭비되거나 Subscribe 타이밍이 어긋날 수 있다.
- Hot 변환 오퍼레이터는 몇 개 있지만, Publish() + RefCount()의 조합이 편리하다 (만능은 아니다)

참고 [Introduction Rx - Hot and Cold observables](http://introtorx.com/Content/v1.0.10621.0/14_HotAndColdObservables.html#HotAndCold)