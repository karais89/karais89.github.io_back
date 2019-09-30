---
layout: post
title: "Animator의 Trigger를 Bool을 사용하여 재현하자"
excerpt: "Animator의 Trigger를 Bool을 사용하여 재현하자 번역"
date: 2019-09-30 22:50:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/72c8950b53f79fadc4af](https://qiita.com/toRisouP/items/72c8950b53f79fadc4af)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

## 하고 싶은 일

- PhotonAnimatorView를 사용하여 Trigger 동기화하고 싶다.
- 그러나 현재 Trigger 동기화는 지원하지 않는다.
- 어쩔 수 없기 때문에 Animator의 Bool을 사용하여 Trigger 기능을 재현한다.

## 원래 "Trigger"는

- 기본은 Bool과 같다
- 그러나 True가 되고 자동으로 False로 돌아 오는 성질이 있다.
- 잠깐 작동하는 애니메이션 전환 등에 자주 사용 된다.

**즉, Bool을 True로 설정하고 다음 프레임에서 False로 변환하면 Trigger과 같은 기능을 할 수 있다.**

## 만들어 보았다

[저는 뭐든지 컴포넌트로 분할하는 파이기 때문에,](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8_%EA%B8%B0%EB%B0%98_%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EA%B3%B5%ED%95%99) 이번에는 애니메이션 관리 컴포넌트를 만들고, 거기에 Trigger 기능을 가진 메소드를 만들었습니다. (컴포넌트 개발 지향)

밖에서 "SetTriggerAttackA", "SetTriggerAttackB"를 실행하면 애니메이션이 전환하도록 하고 있습니다.

```csharp
using System;
using UniRx;
using UnityEngine;

[RequireComponent(typeof(Animator))]
public class PlayerAnimationController : MonoBehaviour
{
    private enum AnimatorParameters
    {
        IsAttackA,
        IsAttackB
    }

    private Animator _animator;

    /// <summary>
    /// Action 대리자를 내보내는 Subject
    /// </summary>
    private Subject<Action> _actionSubject = new Subject<Action>();

    private void Start()
    {
        _animator = GetComponent<Animator>();

        // 1 프레임 지연시켜 Action 대리자를 실행한다.
        _actionSubject
            .DelayFrame(1)
            .Subscribe(x => x());
    }

    /// <summary>
    /// 공격 애니메이션 A 시작
    /// </summary>
    public void SetTriggerAttackA()
    {
        // IsAttackATrigger을 true로 설정
        _animator.SetBool(AnimatorParameters.IsAttackA.ToString(), true);
        
        // IsAttackATrigger을 다음 프레임에서 false로하는 
        _actionSubject.OnNext(() => _animator.SetBool(AnimatorParameters.IsAttackA.ToString(), false));
    }
    
    /// <summary>
    /// 공격 애니메이션 B 시작
    /// </summary>
    public void SetTriggerAttackB()
    {
        // IsAttackBTrigger을 true로 설정
        _animator.SetBool(AnimatorParameters.IsAttackB.ToString(), true);
        
        // IsAttackBTrigger을 다음 프레임에서 false로하는 
        _actionSubject.OnNext(() => _animator.SetBool(AnimatorParameters.IsAttackB.ToString(), false));
    }
}
```

## 처리를 다음 프레임으로 미루는 방법

**"True로 변경 한 후 다음 프레임에서 False로 변경 한다."** 라는 처리는 UniRx를 사용하여 구현 하였습니다.

Action 대리자를 내보내는 Subject를 생성하고 거기에 DelayFrame(1)을 사이에 두는 것으로 프레임을 지연시켜 Subscribe에서 Action 대리자를 실행하도록 하고 있습니다.

UniRx를 사용한 지연 실행

```csharp
private void Start()
{
    _animator = GetComponent<Animator>();

    // 1 프레임 지연시켜 Action 대리자를 실행한다.
    _actionSubject
        .DelayFrame(1)
        .Subscribe(x => x());

    Debug.Log($"Start 실행 : {Time.frameCount}");

    // _ actionSubject 흘린 Action 대리자가 다음 프레임에서 실행되는 
    _actionSubject
        .OnNext(() => Debug.Log($"1 프레임 후에 실행 : {Time.frameCount}"));
}
```

실행결과

    Start 실행 : 1
    1 프레임 후에 실행 : 3

[역주] 포스트에서는 1 프레임 후에 실행 결과가 2로 나오지만, 실제 실행시에 3으로 출력 됩니다. 이 부분은 확인이 필요할 것 같습니다.

## 정리

제대로 Trigger이 작동 하였습니다.

역시 UniRx는 편리합니다. (이번 사용법은 상당히 변칙적인 방법이라고 생각합니다만..)

참고로 이 방법으로 만든 Trigger (내용은 단순히 Bool)은 PhotonAnimatorView에 작동시켰는데, 제대로 작동하였습니다.

단지 동기화 해주지 않는 타이밍이 있기 때문에 조사가 필요할 것 같습니다.

## 15/03/29 추가

다음 프레임 처리를 실행한다면 Observable.NextFrame를 사용하는 것이 더 좋을 것 같습니다.

```csharp
public void SetTriggerAttackB()
{
    _animator.SetBool(AnimatorParameters.IsAttackB.ToString(), true);
    Observable.NextFrame()
        .Subscribe(_ => _animator.SetBool(AnimatorParameters.IsAttackB.ToString(), false));
}
```