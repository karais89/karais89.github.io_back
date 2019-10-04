---
layout: post
title: "StateMachineBehaviour에서 Animator를 감시하자"
excerpt: "StateMachineBehaviour에서 Animator를 감시하자 번역"
date: 2019-10-05 08:17:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/b6540b7f514d18b9a426](https://qiita.com/toRisouP/items/b6540b7f514d18b9a426)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

## StateMachineBehaviour는

Unity5 이상 버전 부터 AnimatorController에 붙일 수 있는 StateMachineBehaviour 스크립트가 추가되었습니다.

이 StateMachineBehaviour는 Animator 상태 머신의 상태 변화에 맞게 CallBack 함수를 실행해주는 방식으로 구성되어 있습니다.

StateMachineBehaviour 샘플
```csharp
using UnityEngine;

public class StateMachineExample : StateMachineBehaviour
{
    public override void OnStateEnter(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        // 새로운 상태로 변할 때 실행
    }

    public override void OnStateUpdate(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        // 처음과 마지막 프레임을 제외한 각 프레임 단위로 실행
    }

    public override void OnStateExit(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        // 상태가 다음 상태로 바뀌기 직전에 실행
    }

    public override void OnStateMove(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        // MonoBehaviour.OnAnimatorMove 직후에 실행
    }

    public override void OnStateIK(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        // MonoBehaviour.OnAnimatorIK 직후에 실행
    }

    public override void OnStateMachineEnter(Animator animator, int stateMachinePathHash)
    {
        // 스크립트가 부착된 상태 기계로 전환이 왔을때 실행
    }

    public override void OnStateMachineExit(Animator animator, int stateMachinePathHash)
    {
        // 스크립트가 부착된 상태 기계에서 빠져나올때 실행
    }
}
```
## StateMachineBehaviour 사용

StateMachineBehaviour는 보통의 Component와 달리 Animator 레이어에 붙여 사용합니다.

![](/images/unity3d/2019-10-05-1.png)

일반 MonoBehaviour 스크립트에서 호출할때는 아래와 같은 방법으로 Animator의 GetBehaviour을 호출하여 가져 옵니다.

StateMachineBehaviour의 취득 방법
```csharp
private Animator _animator;
private StateMachineExample _stateMachineExample;

void Start()
{
    _animator = GetComponent<Animator>();
    _stateMachineExample = _animator.GetBehaviour<StateMachineExample>();
}
```

<br />

## (여담) UniRx를 사용하여 Observable로 상태 변화를 모니터링 할 수 있도록 했다

콜백 그대로 사용하는 것은 조금 불편한 부분이 있어서, [UniRx](https://github.com/neuecc/UniRx)를 사용하여 콜백을 Observable로 변환 해서 사용 합니다.
```csharp
using System;
using UniRx;
using UnityEngine;

public class StateMachineObservables : StateMachineBehaviour
{
    #region OnStateEnter
    private Subject<AnimatorStateInfo> onStateEnterSubject = new Subject<AnimatorStateInfo>();

    public IObservable<AnimatorStateInfo> OnStateEnterObservable => onStateEnterSubject;

    public override void OnStateEnter(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        onStateEnterSubject.OnNext(stateInfo);
    }
    #endregion
    
    #region OnStateExit
    private Subject<AnimatorStateInfo> onStateExitSubject = new Subject<AnimatorStateInfo>();

    public IObservable<AnimatorStateInfo> OnStateExitObservable => onStateExitSubject;

    public override void OnStateExit(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        onStateExitSubject.OnNext(stateInfo);
    }
    #endregion

    #region OnStateMachineEnter
    private Subject<int> onStateMachineEnterSubject = new Subject<int>();

    public IObservable<int> OnStateMachineEnterObservable => onStateMachineEnterSubject;

    public override void OnStateMachineEnter(Animator animator, int stateMachinePathHash)
    {
        onStateMachineEnterSubject.OnNext(stateMachinePathHash);
    }
    #endregion
    
    #region OnStateMachineExit
    private Subject<int> onStateMachineExitSubject = new Subject<int>();

    public IObservable<int> OnStateMachineExitObservable => onStateMachineExitSubject;

    public override void OnStateMachineExit(Animator animator, int stateMachinePathHash)
    {
        onStateMachineExitSubject.OnNext(stateMachinePathHash);
    }
    #endregion
    
    #region OnStateMove
    private Subject<AnimatorStateInfo> onStateMoveSubject = new Subject<AnimatorStateInfo>();

    public IObservable<AnimatorStateInfo> OnStateMoveObservable => onStateMoveSubject;

    public override void OnStateMove(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        onStateMoveSubject.OnNext(stateInfo);
    }
    #endregion
    
    #region OnStateIK
    private Subject<AnimatorStateInfo> onStateIKSubject = new Subject<AnimatorStateInfo>();

    public IObservable<AnimatorStateInfo> OnStateIKObservable => onStateIKSubject;

    public override void OnStateIK(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
    {
        onStateIKSubject.OnNext(stateInfo);
    }
    #endregion
}
```
이후 아래와 같이 사용하고 싶은 Observable을 취득해서 Subscribe 하면 사용할 수 있습니다.

Observable로 스테이트 변화를 감지한후 [shortNameHash](https://docs.unity3d.com/kr/current/ScriptReference/AnimatorStateInfo-shortNameHash.html)를 출력하는 예제

```csharp
private Animator _animator;
private StateMachineObservables _stateMachineObservables;

private void Start()
{
    _animator = GetComponent<Animator>();
    _stateMachineObservables = _animator.GetBehaviour<StateMachineObservables>();
    
    // 시작한 애니메이션의 shortNameHash를 보여준다.
    _stateMachineObservables
        .OnStateEnterObservable
        .Subscribe(stateInfo => Debug.Log(stateInfo.shortNameHash));
}
```

<br />

## UniRx에서 상태 변화를 제어해 보자

"Idle 애니메이션이 5초 이상 재생되면 Rest 상태로 변경 되는 로직"

은 상태 변화를 방금 전의 StateMachineObservables를 사용하면 이런 식으로 작성할 수 있습니다.

```csharp
private Animator _animator;
private StateMachineObservables _stateMachineObservables;

private void Start()
{
    _animator = GetComponent<Animator>();
    _stateMachineObservables = _animator.GetBehaviour<StateMachineObservables>();
    
    _stateMachineObservables
        .OnStateEnterObservable                           // 상태 변화를 감지
        .Throttle(TimeSpan.FromSeconds(5))                // 마지막 상태 변화 후 5초 경과했을 때
        .Where(x => x.IsName("Base Layer.Idle"))          // 현재 재싱중인 애니메이션이 Base Layer의 Idle이라면
        .Subscribe(_ => _animator.SetBool("Rest", true)); // Animator의 Rest 파라미터를 True로 한다.
}
```

![](/images/unity3d/2019-10-05-2.png)

Idle (기본) 상태에서 5 초 가 지나면 Rest (휴식) 모션을 재생하도록 할 수 있었습니다.

<br />

## [역주] 추가내용 (19.09.28)

UniRx가 업데이트 되면서, 기본적으로 제공되는 Trigger 클래스들이 추가되고, 값의 변화를 감지하는 부분을 제공되는 클래스들을 사용하여 구현할 수 있게 되었습니다. [ObservableStateMachineTrigger](https://github.com/neuecc/UniRx/blob/master/Assets/Plugins/UniRx/Scripts/UnityEngineBridge/Triggers/ObservableStateMachineTrigger.cs) 스크립트를 사용하면 위 내용과 동일한 내용을 클래스 구현 없이 사용 가능 합니다.

```csharp
// Unity5 버전 이상부터 사용 가능하다.
#if !(UNITY_4_7 || UNITY_4_6 || UNITY_4_5 || UNITY_4_4 || UNITY_4_3 || UNITY_4_2 || UNITY_4_1 || UNITY_4_0_1 || UNITY_4_0 || UNITY_3_5 || UNITY_3_4 || UNITY_3_3 || UNITY_3_2 || UNITY_3_1 || UNITY_3_0_0 || UNITY_3_0 || UNITY_2_6_1 || UNITY_2_6)

using System; // 윈도우 유니버셜 앱에서 요구된다.
using UnityEngine;

namespace UniRx.Triggers
{
    [DisallowMultipleComponent]
    public class ObservableStateMachineTrigger : StateMachineBehaviour
    {
        public class OnStateInfo
        {
            public Animator Animator { get; private set; }
            public AnimatorStateInfo StateInfo { get; private set; }
            public int LayerIndex { get; private set; }

            public OnStateInfo(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
            {
                Animator = animator;
                StateInfo = stateInfo;
                LayerIndex = layerIndex;
            }
        }

        public class OnStateMachineInfo
        {
            public Animator Animator { get; private set; }
            public int StateMachinePathHash { get; private set; }

            public OnStateMachineInfo(Animator animator, int stateMachinePathHash)
            {
                Animator = animator;
                StateMachinePathHash = stateMachinePathHash;
            }
        }

        // OnStateExit

        Subject<OnStateInfo> onStateExit;

        public override void OnStateExit(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
        {
            if (onStateExit != null) onStateExit.OnNext(new OnStateInfo(animator, stateInfo, layerIndex));
        }

        public IObservable<OnStateInfo> OnStateExitAsObservable()
        {
            return onStateExit ?? (onStateExit = new Subject<OnStateInfo>());
        }

        // OnStateEnter

        Subject<OnStateInfo> onStateEnter;

        public override void OnStateEnter(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
        {
            if (onStateEnter != null) onStateEnter.OnNext(new OnStateInfo(animator, stateInfo, layerIndex));
        }

        public IObservable<OnStateInfo> OnStateEnterAsObservable()
        {
            return onStateEnter ?? (onStateEnter = new Subject<OnStateInfo>());
        }

        // OnStateIK

        Subject<OnStateInfo> onStateIK;

        public override void OnStateIK(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
        {
            if(onStateIK !=null) onStateIK.OnNext(new OnStateInfo(animator, stateInfo, layerIndex));
        }

        public IObservable<OnStateInfo> OnStateIKAsObservable()
        {
            return onStateIK ?? (onStateIK = new Subject<OnStateInfo>());
        }

        // OnStateMove에 영향을 미치지 않습니다.
        // ObservableStateMachine Trigger는 애니메이션을 중지 시킵니다.
        // OnAnimatorMove를 정의함으로써 루트 객체의 움직임을 가로 채서 직접 적용하고 싶다는 의미입니다.
        // http://fogbugz.unity3d.com/default.asp?700990_9jqaim4ev33i8e9h

        //// OnStateMove

        //Subject<OnStateInfo> onStateMove;

        //public override void OnStateMove(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
        //{
        //    if (onStateMove != null) onStateMove.OnNext(new OnStateInfo(animator, stateInfo, layerIndex));
        //}

        //public IObservable<OnStateInfo> OnStateMoveAsObservable()
        //{
        //    return onStateMove ?? (onStateMove = new Subject<OnStateInfo>());
        //}

        // OnStateUpdate

        Subject<OnStateInfo> onStateUpdate;

        public override void OnStateUpdate(Animator animator, AnimatorStateInfo stateInfo, int layerIndex)
        {
            if (onStateUpdate != null) onStateUpdate.OnNext(new OnStateInfo(animator, stateInfo, layerIndex));
        }

        public IObservable<OnStateInfo> OnStateUpdateAsObservable()
        {
            return onStateUpdate ?? (onStateUpdate = new Subject<OnStateInfo>());
        }

        // OnStateMachineEnter

        Subject<OnStateMachineInfo> onStateMachineEnter;

        public override void OnStateMachineEnter(Animator animator, int stateMachinePathHash)
        {
            if (onStateMachineEnter != null) onStateMachineEnter.OnNext(new OnStateMachineInfo(animator, stateMachinePathHash));
        }

        public IObservable<OnStateMachineInfo> OnStateMachineEnterAsObservable()
        {
            return onStateMachineEnter ?? (onStateMachineEnter = new Subject<OnStateMachineInfo>());
        }

        // OnStateMachineExit

        Subject<OnStateMachineInfo> onStateMachineExit;

        public override void OnStateMachineExit(Animator animator, int stateMachinePathHash)
        {
            if (onStateMachineExit != null) onStateMachineExit.OnNext(new OnStateMachineInfo(animator, stateMachinePathHash));
        }

        public IObservable<OnStateMachineInfo> OnStateMachineExitAsObservable()
        {
            return onStateMachineExit ?? (onStateMachineExit = new Subject<OnStateMachineInfo>());
        }
    }
}

#endif
```

<br />

## 참고

- [State Machine Behaviours](https://unity3d.com/kr/learn/tutorials/modules/beginner/5-pre-order-beta/state-machine-behaviours)

<br />

## 사용한 것

- [UniRx](https://github.com/neuecc/UniRx)
- [유니티짱](http://unity-chan.com/)

![](/images/unity3d/2019-10-05-3.jpg)