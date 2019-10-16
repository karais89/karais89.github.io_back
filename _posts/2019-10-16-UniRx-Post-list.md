---
layout: post
title: "UniRx 대해 쓴 포스트를 정리해 보았다"
excerpt: "UniRx 대해 쓴 포스트를 정리해 보았다 번역"
date: 2019-10-16 22:01:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

---

## 요약

과거에 자신이 쓴 UniRx (Rx 포함)의 포스트 목록을 작성해 보았습니다.

여러분에게 도움이 되면 좋겠다고 생각합니다.

포스트를 쓴 당시와 비교하면 현재는 더 좋은 구현 방법이 있는 포스트도 있습니다.

## UniRx 입문 시리즈

- [UniRx 소개 목차](https://qiita.com/toRisouP/items/00b8a5bb8e7b68e0686c#_reference-222cc98a4173c17567a2)

## 과거에 만든 슬라이드

- [미래의 프로그래밍 기술을 Unity에서 -UniRx-](https://www.slideshare.net/torisoup/unity-unirx)
- [처음 UniRx](https://www.slideshare.net/torisoup/uni-rx)

## 역방향

- [UniRx 오퍼레이터 역방향](https://qiita.com/toRisouP/items/3cf1c9be3c37e7609a2f)

## Rx의 기본 사항

## Hot/Cold에 대해

- [Hot과 Cold에 대해]({% post_url 2019-09-29-UniRx-Hot-Cold %})
- [Hot 변환은 어떤 때에 필요한가?]({% post_url 2019-10-13-UniRx-When-is-a-Hot-Conversion %})
- [Multicast의 쓰임새](https://qiita.com/toRisouP/items/fb8034a8b1d8775de9a1)

Rx의 까다로운 성격 중 1개인, Observable의 Hot/Cold에 대해서 정리한 것입니다.

UniRx를 중심으로 한 설계를 진행하는 경우 이 성질의 이해는 필수입니다.

## 기타 Rx 설명

- [Repeat는 무엇인가?]({% post_url 2019-10-12-UniRx-Repeat %})
- [스트림의 메시지를 서로 비교하기](https://qiita.com/toRisouP/items/f5cb995d811e6bcef293)

PairWise/Buffer(2,1)에서 끝나는 곳을 Zip과 Skip(1)에서 쓰는 사람이 많다는 인상을 받습니다.

## UniRx 기능 소개

## Update()를 UniRx로 재 작성

- [Update()를 Observable로 변환하는 방법]({% post_url 2019-10-10-UniRx-How-to-convert-Update-to-Observable %})
- [ObserveEveryValueChanged 프레임간에 값의 변동을 감시하자]({% post_url 2019-10-05-UniRx-ObservableValueChanged %})

UniRx를 도입하면 가장 먼저 해야 할 것 입니다. Update()의 재 작성 방법에 대해 정리해 보았습니다.

우선 `UpdateAsObservable` 과 `ObserveEveryValueChanged` 를 사용하면 좋을 것으로 생각합니다.

## 코루틴과 함께한 예

- [UniRx와 코루틴]({% post_url 2019-10-13-UniRx-Coroutine %})

UniRx와 코루틴의 조합함으로써 진가를 발휘한다고 해도 과언이 아닙니다.

코루틴과의 조합법에 능숙해 지면 좋을것으로 생각합니다.

## 기타

- [연속된 OnNext의 최초 값만 흘리는 오퍼레이터 ThrottleFirst]({% post_url 2019-10-12-UniRx-ThrottleFirst %})
- [Subscribe의 Dispose를 GameObject에 연결 시킨다]({% post_url 2019-10-10-UniRx-Connect-the-Dispose-of-the-Subscribe-to-the-GameObject %})
- [.NET 4.6 시대의 Unity에서 UniRx](https://qiita.com/toRisouP/items/596e42fac1b02f59f271)
- [[UniRx] ReactiveCommand/AsyncReactiveCommand 대해](https://qiita.com/toRisouP/items/c6fba9f01e6d15dabd79)
- [UniRx의 ObjectPool을 이용하는](https://qiita.com/toRisouP/items/2a5fb86654525a4a8453)

AddTo는 자주 사용하는 기능이므로 기억해야 합니다.

## 예제 요약

## 판정의 개선

- [CharacterController IsGrounded 판정을 개선하기]({% post_url 2019-09-15-UniRx-Example-IsGrounded %})

솔직히 Raycast를 사용하는게 더 안정적입니다.

- [마우스 누르는것을 판정](https://qiita.com/toRisouP/items/4fec0e9716be4d415798)

코루틴으로 쓰는 것이 복잡하지만 나중에 확장하기 쉽고 좋을지도 모르겠습니다.

## PhotonCloud의 활용도를 높이는

- [UniRx를 사용하여 Photon Cloud의 RoomList의 업데이트 모니터링]({% post_url 2019-09-30-UniRx-Photon-Cloud-RoomList %})
- [PhotonCloud 로그인 처리를 UniRx로 동기처리 처럼 쓰기]({% post_url 2019-10-13-UniRx-PhotonCloud-Login-Process-like-sync %})
- [[PUN] PhotonCloud 콜백 처리를 UniRx로 지능적으로 쓴다 "PhotonRx"](https://qiita.com/toRisouP/items/10d9112eda30a0ba9278)

PhotonRx는 Photon을 사용하기 쉽게 하기 위한 하나의 해결책 인 것 같습니다.

## 다른 구현 예

- [UniRx를 사용하여 동시에 화면에 비친 객체의 수를 세기]({% post_url 2019-09-14-UniRx-Example-View-Screen-Count %})
- [UniRx에서 카운트 다운 타이머를 만들자]({% post_url 2019-09-29-UniRx-Count-Down-Timer %})
- [Animator의 Trigger을 Bool을 사용하여 재현하자]({% post_url 2019-09-30-UniRx-Animator-Trigger-Bool %})
- [StateMachineBehaviour에서 Animator를 감시하자]({% post_url 2019-10-05-UniRx-StateMachineBehaviour-Animator %})
- [스크립트 처리 시점을 조작한다]({% post_url 2019-10-12-UniRx-Script-Processing-Time %})
- [FPS 카운터](https://qiita.com/toRisouP/items/1d0682e7a35cdb04bc38)

"일단 시도 해 보았다" 정도의 포스트 입니다. 너무 깊게 받아들이지 마세요.