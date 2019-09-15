---
layout: post
title: "CharacterController IsGrounded 판정을 개선하기"
excerpt: "CharacterController IsGrounded 판정을 개선하기 번역"
date: 2019-09-15 20:10:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/83fd28b6d4a70a7ed1d2](https://qiita.com/toRisouP/items/83fd28b6d4a70a7ed1d2)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

Unity 개발에서 플레이어 캐릭터를 만들 때 자주 사용되는 컴포넌트에 [CharacterControoler](https://docs.unity3d.com/kr/current/Manual/class-CharacterController.html)가 있습니다.

이 컴포넌트는 개체를 이동할 때 바닥과 벽의 판정, 언덕의 경사 및 계단 오르기 등의 판정을 쉽게 계산 해주는 매우 유용한 컴포넌트 입니다.

다만 이 Character Controller은 **접지 판정의 IsGrounded의 정밀도**가 별로 입니다.

언덕길이나 계단을 이동하는 동안 분명히 접지하고 있음에도 불구하고 false 판정이 리턴되곤 합니다.

따라서 IsGrounded의 기준으로 점프 여부를 결정하면 점프를 하지 못하는 경우가 생길 수 있습니다.

![](/images/unity3d/2019-09-15-1.jpeg)

(경사면을 이동중에는 isGrounded가 true/false가 격렬하게 변한다)

그래서 이번에는 이 IsGrounded의 판정을 완만하게 하여 개선해 보려고 합니다.

## 방법 1: Raycast와 병행하여 판정을 정확하게 하기

---

IsGrounded는 엄격하게 딱 바닥에 접해 있지 않으면 true가 되지 않습니다.

그래서 이 판정을 어느 정도 바닥에 가까우면 "지면에 접하고 있다"로 판정하도록 하려고 합니다.

판정에 [Physics.Raycast](https://docs.unity3d.com/ScriptReference/Physics.Raycast.html) 라는 개체와의 충돌을 검사하는 API을 이용합니다.

(이름 그대로 점에서 가상의 광선을 쏘아 그것이 물체에 닿았는지 확인 하는 API입니다.)

이 Raycast를 GameObject의 발밑에서 발사하고 Raycast가 지상과 충돌 여부에 지면에 접하고 있는지 판정해 보겠습니다.

![](/images/unity3d/2019-09-15-2.jpeg)

(개체의 바닥에서 바로 밑에 Raycast를 쏘아서 충돌 여부를 판단)

CheckGroundedWithRaycast.cs
```csharp
/// <summary>
/// 땅에 접지되어 있는지 여부를 확인
/// Update에서 실행, _characterController, _fieldLayer의 경우 Start 메서드에서 캐시 처리.
/// </summary>
/// <returns></returns>
private bool IsCheckGrounded()
{
    // CharacterController.IsGrounded가 true라면 Raycast를 사용하지 않고 판정 종료
    if (_characterController.isGrounded) return true;
    // 발사하는 광선의 초기 위치와 방향
    // 약간 신체에 박혀 있는 위치로부터 발사하지 않으면 제대로 판정할 수 없을 때가 있다.
    var ray = new Ray(this.transform.position + Vector3.up * 0.1f, Vector3.down);
    // 탐색 거리
    var maxDistance = 1.5f;
    // 광선 디버그 용도
    Debug.DrawRay(transform.position + Vector3.up * 0.1f, Vector3.down * maxDistance, Color.red);
    // Raycast의 hit 여부로 판정
    // 지상에만 충돌로 레이어를 지정
    return Physics.Raycast(ray, maxDistance, _fieldLayer);
}
```

이와 같이 CharacterController.ISGrounded과 Raycast를 병행함으로써 경사면의 행동을 개선 한 수 있습니다. 그러나 이 CheckGround()는 실행할 때마다 매번 Raycast를 실행하게 됩니다. 실제로 사용할 때는 Raycast 결과를 프레임별로 캐시하면 더 좋지 않을까 생각합니다. (실제 캐시 처리를 할 수 있는지  의문이 듬)

## 방법 2: IsGrounded 값의 변동이 안정 될 때까지 IsGrounded 값을 무시

---

IsGrounded 값을 잘 관찰하면, "점프와 착지 직후" "경사면을 이동하는 동안" true/false 값이 수차례 변동하는 것을 알 수 있습니다. 그래서 이 값이 변동하는 것을 일정 시간 무시하고 값이 안정화 된 이후에 이용하도록 해봅시다. (마지막에 값이 변화하고 n 밀리 초 경과했을 때 그 값으로 결정합니다)

이러한 시간에 관한 판정 처리는 Rx가 적합하므로, Rx의 Unity용 구현 [UniRx](https://github.com/neuecc/UniRx)를 사용해 봅시다.

CheckGroundedComponent.cs
```csharp
using UniRx;
using UnityEngine;

public class CheckGroundedWithRx : MonoBehaviour
{
    private bool _isGrounded;

    public bool IsGrounded { get => _isGrounded; }
    
    private void Start()
    {
        _characterController = GetComponent<CharacterController>();
        _characterController
                        .ObserveEveryValueChanged(x => x.isGrounded)
            .ThrottleFrame(5)
            .Subscribe(x => _isGrounded = x);
    }
}
```

CharacterController.IsGrounded을 배치하고 값이 마지막으로 변화하고 5 프레임 안정 될때까지 그 값을 무시하도록 해 보았습니다.

이러한 "값의 변화를 감시한다", "시간을 사용하여 판정"등 처리는 Rx를 사용하면 정말 쉽게 작성할 수 있으므로 사용하는 것을 추천 합니다.

다만 학습 비용 (러닝커브)는 상당히 높습니다.

## 마지막으로

---

UniRx가 더 유행했으면 좋겠습니다.