---
layout: post
title: "UniRx에서 카운트 다운 타이머를 만들자"
excerpt: "UniRx에서 카운트 다운 타이머를 만들자 번역"
date: 2019-09-29 22:44:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/581ffc0ddce7090b275b](https://qiita.com/toRisouP/items/581ffc0ddce7090b275b)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

Unity 개발에서 카운트 다운 타이머 (또는 카운트 업 타이머)를 구현해야 되는 경우가 많습니다.

이번에는 그 카운트 다운 타이머를 UniRx를 사용하여 구현 할 예정입니다.

## Rx로 만든 카운트 다운 타이머

지정한 초만큼 카운트 다운하는 스트림
```csharp
/// <summary>
/// countTime 만큼 카운트 다운하는 스트림
/// </summary>
/// <param name="countTime"></param>
/// <returns></returns>
private IObservable<int> CreateCountDownObservable(int countTime)
{
    return Observable
        .Timer(TimeSpan.FromSeconds(0), TimeSpan.FromSeconds(1)) // 0초 이후 1초 간격으로 실행
        .Select(x => (int) (countTime - x)) // x는 시작하고 나서의 시간(초)
        .TakeWhile(x => x > 0); // 0초 초과 동안 OnNext 0이 되면 OnComplete
}
```
Rx로 초를 카운트 다운하는 경우 위와 같은 방법으로 구현할 수 있습니다.

([Generate](http://reactivex.io/documentation/operators/create.html) 및 [plan/Pattern](http://reactivex.io/documentation/ko/operators/and-then-when.html)를 사용해도 좋지만, UniRx에는 이러한 기능이 아직 구현되지 않았다.)

다만, 이 CreateCountDownObservable로 만든 스트림은 **Cold**라는 점에 주의할 필요가 있습니다.

(Subscribe한 타이밍부터 시작하고 카운트다운이 시작되는 점, 1개의 타이머 스트림을 여러번 Subscribe하려면 Hot 변환이 필요하다는 점 등)

Hot/Cold 내용은 [여기](https://qiita.com/toRisouP/items/f6088963037bfda658d3)를 참고하십시오.

## Rx에서 타이머를 만드는 장점/단점

Rx에서 타이머를 만드는 것과 Unity 표준 코루틴과 InvokeRepeat에서 타이머를 만드는 것을 비교하면 Rx로 만드는 경우 다음과 같은 장단점이 있습니다.

**장점**

- 스트림 자체가 시간을 관리 해준다.
- 타이머를 감시하는 측의 처리가 쉽다.
- 타이머를 바탕으로 다른 작업을 시작 하기 쉽다.

단점

- 어느 순간에 타이머의 시간을 얻을 수 없다 (임시 변수에 저장할 필요가 있다)
- 타이머를 중단하거나 남은 시간을 고쳐 쓰기가 어렵다 (불가능하지는 않다)
- Rx 자체가 어렵고 이해하기 어렵다 (Hot/Cold를 알지 못하면 오동작 가능성이 있다)

타이머를 Rx에서 만드는 가장 큰 장점은 **"타이머를 감시하는 측의 처리가 쉽다"**와 **"타이머를 바탕으로 다른 작업을 시작하기 쉽다"** 입니다.

## 예) Rx 타이머의 값을 사용하여 작업

예를 들어, 방금 전의 CountDownTimer을 이용하여 다음과 같은 기능을 구현하려고 합니다.

**구현 목록**

- Start() 시점에서 60초를 카운트 한다.
- 타이머의 숫자를 UnityEngine.UI.Text에 그린다.
- 카운트가 10초 이하가 되면 위의 Unity.UI.Text의 글자의 색상을 붉은 색으로 변하게 한다.
- 카운트가 10초 이하가 되면 카운트마다 효과음을 울린다.
- 계산이 끝나면 Unity.UI.Text의 문자를 지운다.
- 계산이 끝나면 효과음을 울린다.

RxCountDownTimer.cs
```csharp
/// <summary>
/// 카운트 구성 요소
/// </summary>
public class RxCountDownTimer : MonoBehaviour
{
    /// <summary>
    /// 카운트 다운 스트림
    /// 이 Observable을 각 클래스가 Subscribe 한다.
    /// </summary>
    public IObservable<int> CountDownObservable => _countDownObservable.AsObservable();
    
    private IConnectableObservable<int> _countDownObservable;

    // 60초 카운트 스트림을 생성
    // Publish로 Hot 변환
    private void Awake() => 
        _countDownObservable = CreateCountDownObservable(60).Publish();

    // start시 카운트 시작
    private void Start() =>
        _countDownObservable.Connect();

    /// <summary>
    /// countTime 만큼 카운트 다운하는 스트림
    /// </summary>
    /// <param name="countTime"></param>
    /// <returns></returns>
    private IObservable<int> CreateCountDownObservable(int countTime) =>
        Observable
            .Timer(TimeSpan.FromSeconds(0), TimeSpan.FromSeconds(1))
            .Select(x => (int) (countTime - x))
            .TakeWhile(x => x > 0);
}
```

CountDownTextComponent.cs
```csharp
/// <summary>
/// 타이머의 시간을 기초로 Text를 업데이트 할 컴포넌트
/// </summary>
public class CountDownTextComponent : MonoBehaviour
{
    /// <summary>
    /// UnityEditor에서 할당 한다.
    /// </summary>
    [SerializeField] private RxCountDownTimer _rxCountDownTimer;

    /// <summary>
    /// uGUI의 Text
    /// </summary>
    private Text _text;

    private void Start()
    {
        _text = GetComponent<Text>();
        
        // 타이머의 남은 시간을 표시한다.
        _rxCountDownTimer
            .CountDownObservable
            .Subscribe(time =>
            {
                // onNext에서 시간을 표시한다.
                _text.text = $"남은 시간 : {time}";
            }, () =>
            {
                // onComplete에서 문자를 지운다.
                _text.text = string.Empty;
            }).AddTo(gameObject);
        
        // 타이머가 10초 이하로 되는 타이밍에 색을 붉은 색으로 한다.
        _rxCountDownTimer
            .CountDownObservable
            .First(timer => timer <= 10)
            .Subscribe(_ => _text.color = Color.red);
    }
}
```
CountDownSoundComponent.cs
```csharp
[RequireComponent(typeof(AudioSource))]
public class CountDownSoundComponent : MonoBehaviour
{
    // 효과음
    [SerializeField] private AudioClip _seCountDownTick;
    [SerializeField] private AudioClip _seCountDownEnd;
    
    private AudioSource _audioSource;

    /// <summary>
    /// UnityEditor에서 할당 한다.
    /// </summary>
    [SerializeField] private RxCountDownTimer _rxCountDownTimer;

    private void Start()
    {
        _audioSource = GetComponent<AudioSource>();
        
        // 카운트가 10초 이하가 되면 효과음을 1초마다 울리게 한다.
        _rxCountDownTimer
            .CountDownObservable
            .Where(time => time <= 10)
            .Subscribe(_ => _audioSource.PlayOneShot(_seCountDownTick));
        
        // 계산이 완료된 시점에서 효과음을 울린다.
        _rxCountDownTimer
            .CountDownObservable
            .Subscribe(_ => { ; }, () => _audioSource.PlayOneShot((_seCountDownEnd)));
    }
}
```
RxCountDownTimer에서 카운트 다운 스트림을 만들고 그것을 CountDownTextComponent와 CountDownSoundComponent에서 모니터링하여 값의 변화와 동시에 처리를 실시하게 구현되어 있습니다.

Rx를 사용하면 "타이머의 현재 시간이 흐른다"는 이벤트와 같은 (라기 보다는 그 이상)것을 보다 적은 코드로 처리할 수 있게 됩니다.

**"값의 변화를 감시하고 처리한다", "변화하는 값이 (복잡한) 조건을 충족했을 때 처리한다"** 라는 부분에서는 Rx를 사용하는 것이 유용합니다.

## 예) 타이머를 바탕으로 복잡한 처리를 한다.

방금 전의 60초 타이머의 동작을 바꾸어 봅시다.

변경할 목록

- Start()시 3초간 카운트 다운을 한다.
- 3초 카운트 다운이 끝나고 나서 60초 카운트 다운을 시작한다.
- 60초 카운트 다운이 끝나고 난 후 5초 정도 기다린 후 다른 씬으로 이동한다.

"게임 시작 전 3초 카운트 다운" → "게임 시작 60초 카운트 다운" → "5초 동안 결과 표시후 게임 종료" 순서라고 생각해 주십시오.

이를 Rx에서 쓰면 다음과 같습니다.
```csharp
public class GameTimerManager : MonoBehaviour
{
    /// <summary>
    /// 경기 시작 전 카운트 다운
    /// </summary>
    public IObservable<int> GameStartCountDownObservable { get; private set; }
    
    /// <summary>
    /// 경기 중 카운트 다운
    /// </summary>
    public IObservable<int> BattleCountDownObservable { get; private set; }

    private void Start()
    {
        // 경기 전 3초 타이머
        // 3초 타이머의 스트림을 Publish로 Hot으로 변환 (아직 Connect는 하지 않는다)
        var startConnectableObservable = CreateCountDownObservable(3).Publish();
        // 외부에 공개하기 위해 Observable로 저장
        GameStartCountDownObservable = startConnectableObservable;
        
        // 경기 중 60초 타이머
        // 60초 타이머의 스트림을 Publish로 Hot으로 변환 (아직 Connect는 하지 않는다)
        var battleConnectableObservable = CreateCountDownObservable(60).Publish();
        // 외부에 공개하기 위해 Observable로 저장
        BattleCountDownObservable = battleConnectableObservable;
        
        // 3초 타이머의 OnComplete에서 60초 타이머를 Connect한다 (60초 타이머 시작)
        GameStartCountDownObservable
            .Subscribe(_ => { ; }, () => battleConnectableObservable.Connect());
        
        // 60초 타이머 뒤에 Concat으로 5초 타이머를 연결하고 OnComplete에서 Scene를 전환한다.
        BattleCountDownObservable
            .Concat(CreateCountDownObservable(5))
            .Subscribe(_ => { ; }, () =>
            {
                SceneManager.LoadScene("NextScene");
            }).AddTo(gameObject);
        
        // 3초 타이머 시작
        startConnectableObservable.Connect();
    }
    
    /// <summary>
    /// countTime 만큼 카운트 다운하는 스트림
    /// </summary>
    /// <param name="countTime"></param>
    /// <returns></returns>
    private IObservable<int> CreateCountDownObservable(int countTime) =>
        Observable
            .Timer(TimeSpan.FromSeconds(0), TimeSpan.FromSeconds(1))
            .Select(x => (int) (countTime - x))
            .TakeWhile(x => x > 0);
}
```
조금 복잡 할지도 모릅니다만, 이 코드만으로 구현이 완료됩니다.

상태를 관리하는 필드 변수는 불 필요 하고, 스트림의 합성만으로 구현할 수 있었습니다.

## 정리

- Rx를 이용하면 타이머를 보다 적은 코드로 구현할 수 있다.
- 스트림 자신이 시간을 유지하기 위해 임시 저장 변수가 불필요 하다. (그러나 임의의 프레임에서의 시간 취득은 할 수 없게 된다)
- 타이머의 복잡한 구현은 Rx의 합성 메서드로 구현 할 수 있다.
- UI 갱신 등에 Rx 스트림을 편리하게 사용할 수 있다. (Model에서 View로 통지)
- Rx의 Hot/Cold의 개념을 알고 있지 않으면 오동작 가능성이 있다.
- Rx 자체가 어렵다.
- UniRx가 더 유행 했으면 좋겠다.