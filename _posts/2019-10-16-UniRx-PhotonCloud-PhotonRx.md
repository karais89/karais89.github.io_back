---
layout: post
title: "PhotonCloud 콜백 처리를 UniRx 지능적으로 쓴다 'PhotonRx'"
excerpt: "PhotonCloud 콜백 처리를 UniRx 지능적으로 쓴다 'PhotonRx' 번역"
date: 2019-10-16 22:24:00 +0900
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
- Photon Unity Networking 2 (2.14)

원문 : [https://qiita.com/toRisouP/items/10d9112eda30a0ba9278](https://qiita.com/toRisouP/items/10d9112eda30a0ba9278)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

## PhotonRx

PUN의 콜백 통지를 UniRx의 ObservableTrigger 형식으로 취급 할 수 있게 만들어 보았습니다.

[PhotonRx](https://github.com/TORISOUP/PhotonRx)

라이센스는 [MIT](https://ko.wikipedia.org/wiki/MIT_%ED%97%88%EA%B0%80%EC%84%9C) 입니다.

[역주] 포톤 v1 기준으로 만들어진 소스입니다. v2에서 사용시에는 해당 소스를 참고하여 변형하면 될 것으로 보입니다.

## 사용법

using에 PhotonRx를 추가하면, 나머지는 this.xxxAsObservable 형식으로 콜백 통지를 Observable로 취급 할 수 있게 됩니다.

```cs
using System;
using PhotonRx; // 추가
using UnityEngine;
using UniRx;

public class PhotonRxSamle : MonoBehaviour {

    void Start ()
    {
        // 플레이어의 입장 이벤트
        this.OnPhotonPlayerConnectedAsObservable()
            .Subscribe(p => Debug.Log(p.name + "님이 입장했습니다."));

        // 플레이어의 퇴장 이벤트
        this.OnPhotonPlayerDisconnectedAsObservable()
            .Subscribe(p => Debug.Log(p.name+"님이 퇴장했습니다."));
    }
}
```

PhotonRx에 정의되어 있는 xxxAsObservable()는 ObservableTriggerBase와 같은 방식으로 작동 됩니다.

즉, 대상의 **GameObject가 삭제되면 자동으로 OnCompleted가 동작**하도록 되어 있기 때문에 스트림의 수명 관리도 생각하지 않아도 됩니다.

## PhotonRx를 도입해야 하는 이유

- Photon 콜백이 IDE 보완이 되는 형태로 호출할 수 있게 된다.
- 형태 안전하게 취급할 수 있게 된다.
- UniRx의 유연한 비동기 처리에 그대로 올릴 수 있다.

## 고급 사용법

PhotonRx와 코루틴을 결합하여 **비동기 처리를 동기처리처럼** 쓸 수 있습니다. (await처럼)

```cs
using System;
using System.Collections;
using PhotonRx;
using UnityEngine;
using UniRx;

public class PhotonRxSamle2 : MonoBehaviour
{

    private void Start()
    {
        // 로비에 연결 처리
        StartCoroutine(ConnectCoroutine());
    }

    private IEnumerator ConnectCoroutine()
    {
        // 로비에 연결 결과를 통지하는 스트림을 생성
        // 연결 성공 · 실패 스트림 2 개를 병합하여 두 가지 이벤트 통지의 도달을 기다리는
        var loginStream = this.OnJoinedLobbyAsObservable().Cast(default(object))
            .Merge(this.OnFailedToConnectToPhotonAsObservable().Cast(default(object)))
            .FirstOrDefault() // OnCompleted를 발화하기 위하여
            .PublishLast(); // PublishLast는 결과를 캐시하는

        // Connect에서 StartAsCoroutine 이전에 스트림 모니터링을 시작
        loginStream.Connect();

        // 연결 시작
        PhotonNetwork.ConnectUsingSettings("0.1");

        // 결과 저장을 위한 객체
        var result = default(object);

        // StartAsCoroutine은 대상 스트림의 OnCompleted가 발행 될 때까지 null를 돌려주는 (코루틴에서 대기)
        yield return loginStream.StartAsCoroutine(x => result = x, ex => { });

        // 결과의 형태를보고 판정
        if (result is DisconnectCause)
        {
            Debug.Log("연결실패");
            //
            // 여기에 실패 처리를 쓰는
            //
            yield break;
        }

        Debug.Log("연결성공");

        // 다음 처리를 계속한다면 여기에 쓰기

    }
}
```

내부는 `StartAsCoroutine` 입니다.

자세한 내용은 [[UniRx] UniRx와 코루틴]({% post_url 2019-10-13-UniRx-Coroutine %})을 참고하시기 바랍니다.

## 만든 경위

15/6/19의 UniRx 연구회에서 강연 된 gloops의 모리나가 씨의 [젋은 엔지니어에서 본 UniRx를 이용한 게임 개발](https://www.slideshare.net/HirohitoMorinaga/unirx) 이라는 슬라이드에서 ObservableTrigger로 PUN의 콜백을 처리하면 편리하고 행복했다는 내용이 있었습니다.

확실히 Trigger 기반으로 해 버리면 다루기 쉽고 성능도 좋기 때문에 이것은 곧 사용할 수 밖에 없다고 생각하고 구현을 보았습니다.