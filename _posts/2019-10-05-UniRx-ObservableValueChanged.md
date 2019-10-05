---
layout: post
title: "ObserveEveryValueChanged 프레임간에 값의 변동을 감시하자"
excerpt: "ObserveEveryValueChanged 프레임간에 값의 변동을 감시하자 번역"
date: 2019-10-05 08:29:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/d0d32cf674a00f3a8427](https://qiita.com/toRisouP/items/d0d32cf674a00f3a8427)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

최근 게시 된 "[미래의 프로그래밍 기술을 Unity에서 -UniRx-](https://www.slideshare.net/torisoup/unity-unirx)"에서 UniRx에서 값의 변동을 모니터링 하는 방법을 소개했습니다.

하지만, [UniRx의 15/03/03 업데이트 내용](https://github.com/neuecc/UniRx/commit/3a60365eae48f216d91fae397315038c0194dea7)에 ObserveEveryValueChanged 라는 기능이 추가되었습니다. 이 기능은 수정 된 값의 변화의 감시를 더 간편하게 사용할 수 있기 때문에 사용법을 소개하고 싶습니다.

## ObserveEveryValueChanged()

ObserveEveryValueChanged는 전체 클래스에 대한 확장 메서드로 정의되며 다음과 같은 특징이 있습니다.

- 임의의 클래스 객체 사용할 수 있다. ~~(그러나 UnityEngine.Object 이외에 적용하는 것은 안전하지 않다.)~~
- 이 인스턴스 객체의 속성 값 (속성뿐만 아니라 값을 반환 하는 것이면 무엇이든)을 이전 프레임과 비교하여 그 값이 변화할때 OnNext를 스트림에 흐르게 된다.
- **1 프레임 내에서 발생한 여러 차례의 변화는 감지하지 못하고 이전 프레임과 현재 프레임의 변화만 감지할 수 있다.**
- 메시지가 흐르는 타이밍은 엄밀하게는 Update()가 아닌 코루틴의 실행 타이밍이 된다. ([참고](https://docs.unity3d.com/Manual/ExecutionOrder.html))
- 대상이 UnityEngine.Object의 파생 개체 인 경우 Destroy시 OnCompleted가 스트림에 흐르게 된다.
- MonoBehaviour 이외의 장소에서도 사용할 수 있다.

## 사용 예) CharacterController.isGrounded를 감시한다.

ObserveEveryValueChanged를 사용한 예
```csharp
// 캐릭터가 착지한 순간을 감지
_characterController
            .ObserveEveryValueChanged(x => x.isGrounded)
            .Where(x => x)
            .Subscribe(_ => Debug.Log("OnGrounded!"));
```
비교) ObserveEveryValueChanged를 사용하지 않는 예 (UpdateAsObservable을 사용한 예)
```csharp
this.UpdateAsObservable()
            .Select(_ => _characterController.isGrounded)
            .DistinctUntilChanged()
            .Where(x => x)
            .Subscribe(_ => Debug.Log("OnGrounded!"));
```
UpdateAsObservable().Select(_ => _characterController.isGrounded).DistinctUntilChanged() 부분이 없어 깔끔하게 코드를 작성 할 수 있었습니다.

## 정리

ObserveEveryValueChanged()를 사용해서 **"매 프레임 값의 변동을 모니터링하고 뭔가하고 싶다"** 라고 하는 것을 더 간결하게 작성할 수 있게 되었습니다.

그러나 ~~UnityEngine.Object 이외의 Class 객체에 ObserEveryValueChanged을 사용하면 수동으로 객체 삭제시 SubScribe의 Dispose를 할 필요가 있으므로 주의가 필요합니다.~~

(추가: 15/03/23 수정되어 일반 인스턴스 객체에 대해서도 폐기시 자동으로 OnCompleted가 불러지게 되었습니다)