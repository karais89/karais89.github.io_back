---
layout: post
title: "UniRx에서 FPS 카운터를 만들어보자"
excerpt: "UniRx에서 FPS 카운터를 만들어보자 번역"
date: 2019-10-23 13:10:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/1d0682e7a35cdb04bc38](https://qiita.com/toRisouP/items/1d0682e7a35cdb04bc38)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

UniRx를 사용해 FPS 카운터를 만들어 보았습니다.

```cs
public class FPSCounter : MonoBehaviour
{
    [SerializeField] private int bufferSize = 5; // 버퍼 사이즈
    public ReadOnlyReactiveProperty<float> FpsReactiveProperty;
    
    private void Awake()
    {
        FpsReactiveProperty = this.UpdateAsObservable()
            .Select(_ => Time.deltaTime) // Time.deltaTime로 변환
            .Buffer(bufferSize, 1) // 지난 bufferSize 분 버퍼
            .Select(x => 1.0f / x.Average()) // 평균에서 fps 산출
            .ToReadOnlyReactiveProperty();

        FpsReactiveProperty.Subscribe(x => Debug.Log(x));
    }
}
```

## 설명

위의 코드는 과거 BufferSize 분의 Time.deltaTime의 평균치를 가지고 거기에서 FPS를 산출하고 있습니다.

그리고 스트림을 ReadOnlyReactiveProperty로 변화하고 public으로 공개하고 있습니다.

주의해야 할 점으로는 FpsReactiveProperty 초기화 타이밍입니다.

FpsReactiveProperty 초기화가 끝나기 전에 다른 컴포넌트가 FpsReactiveProperty를 Subscribe 해 버리면 NullReferenceException이 발생해 버리므로 주의 합시다.

[역주]

ReactiveProperty는 자체적으로 Hot한 성질을 가지고 있기 때문에, FpsReactiveProperty를 사용하는 어느 곳이든 같은 스트림을 사용하게 된다.

## 추기

Static Property로 처리하는 것이 좋겠다는 이야기를 [@neuecc](https://qiita.com/neuecc)씨한테 받아서 그쪽 버전으로 만들어 보았습니다.

```cs
public static class FPSCounter
{
    private const int BufferSize = 5; // 샘플 수를 바꾸려면 여기를 바꾼다.
    public static IReadOnlyReactiveProperty<float> Current { get; private set; }
    
    static FPSCounter() =>
        Current = Observable.EveryUpdate()
            .Select(_ => Time.deltaTime)
            .Buffer(BufferSize, 1)
            .Select(x => 1.0f / x.Average())
            .ToReadOnlyReactiveProperty();
}
```

이 코드의 장점은 MonoBehaviour와 무관한 위치에서 언제든지 호출하여 fps를 확인 할 수 있다는 점입니다.

```cs
FPSCounter.Current.Subscribe(fps => Debug.Log(fps));
```