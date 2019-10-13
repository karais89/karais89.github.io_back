---
layout: post
title: "PhotonCloud 로그인 처리를 UniRx로 동기처리 처럼 쓰기"
excerpt: "PhotonCloud 로그인 처리를 UniRx로 동기처리 처럼 쓰기 번역"
date: 2019-10-13 21:16:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/584d5bc706e4b1dca3bb](https://qiita.com/toRisouP/items/584d5bc706e4b1dca3bb)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

먼저 [[UniRx] UniRx와 코루틴]({% post_url 2019-10-13-UniRx-Coroutine %})을 읽어 주시면 이해하기 쉬울 것으로 생각 합니다.

## 시작하기

[PhotonCloud](https://www.photonengine.com/ko-kr/Photon)는 기본적으로 콜백으로 통지되는 구조로 되어 있습니다.

그 때문에, 처리가 곳곳에 분산 되어, 읽기 힘든 코드가 되는 경향이 있습니다.

이번에는 UniRx의 StartAsCoroutine을 사용하여 Photon 로그인 처리 부분을 동기처리 처럼 써 보겠습니다. 

## Photon 콜백을 Observable로 변환

우선 Photon 콜백을 Observable로 제공하는 싱글톤을 만듭니다.

싱글톤은 [tsubaki 블로그](http://tsubakit1.hateblo.jp/entry/20140709/1404915381)의 SingletonMonoBehaviour를 사용하여 만듭니다.

```cs
/// <summary>
/// Photon 콜백을 Observable로 제공하는 싱글 톤
/// </summary>
public class PhotonCallbacks : SingletonMonoBehaviour<PhotonCallbacks>
{
    #region Server
    /// <summary>
    /// 서버 연결 결과를 통지 할 Subject
    /// </summary>
    private Subject<ConnectSeverResult> _connectToSeverResutlSubject;

    /// <summary>
    /// 서버 연결의 결과를 통지 할
    /// </summary>
    public IObservable<ConnectSeverResult> ConnectToSeverObservable
    {
        get
        {
            if(_connectToSeverResutlSubject==null) _connectToSeverResutlSubject = new Subject<ConnectSeverResult>();
            return _connectToSeverResutlSubject.AsObservable().First(); // OnCompleted를 발행하기위한 First
        }
    } 

    /// <summary>
    /// 서버에 연결 성공
    /// </summary>
    private void OnConnectedToPhoton()
    {
        if(_connectToSeverResutlSubject!=null) _connectToSeverResutlSubject.OnNext(new ConnectSeverSuccess());
    }


    /// <summary>
    /// Photon 서버에 연결해 않았다
    /// </summary>
    /// <param name='cause'>원인</param>
    private void OnFailedToConnectToPhoton(DisconnectCause cause)
    {
        if (_connectToSeverResutlSubject != null) _connectToSeverResutlSubject.OnNext(new ConnectSeverFail(cause));
    }
    #endregion
    
    #region Lobby
    /// <summary>
    /// 로비에 연결 한 것을 통지하는 Subject
    /// </summary>
    Subject<Unit> _onJoinedLobby;

    /// <summary>
    /// 로비 입실 성공을 알린다.
    /// </summary>
    public IObservable<Unit> OnJoinedLobbyAsObservable
    {
        get
        {
            if (_onJoinedLobby == null) _onJoinedLobby = new Subject<Unit>();
            return _onJoinedLobby.First();
        }
    }

    /// <summary>
    /// 로비에 입장 한다.
    /// </summary>
    private void OnJoinedLobby()
    {
        if (_onJoinedLobby != null)
            _onJoinedLobby.OnNext(Unit.Default);
    }
    #endregion
}

/// <summary>
/// 서버 연결의 성공 여부 판정 클래스
/// </summary>
public abstract class ConnectSeverResult { }

/// <summary>
/// 성공
/// </summary>
public class ConnectSeverSuccess : ConnectSeverResult{}

/// <summary>
/// 실패
/// </summary>
public class ConnectSeverFailuer : ConnectSeverResult
{
    private DisconnectCause disconnectCause;
    /// <summary>
    /// 실패 원인
    /// </summary>
    public DisconnectCause Cause { get { return disconnectCause; } }
    public ConnectSeverFailuer(DisconnectCause cause)
    {
        disconnectCause = cause;
    }
}
```

`ConnectToSeverObservable` 를 Subscribe하여 서버 연결 여부에 성패가 흘러 오게 되어 있습니다.

(성공 여부 판정에 `ConnectSeverResult` 라는 클래스를 일부러 만들었습니다. 하지만 이 부분은 더 나은 방법이 있나 고민 중입니다.)

[역주] 아래는 PhotonCloud v2 버전에 맞게 수정 된 스크립트 입니다.

```cs
using System;
using Photon.Realtime;
using UniRx;

// Photon 콜백을 Observable로 제공하는 싱글 톤
public class PhotonCallbacks : PhotonSingletonMonoBehaviourFast<PhotonCallbacks>
{
    #region Server
    /// <summary>
    /// 서버 연결 결과를 통지 할 Subject
    /// </summary>
    private Subject<ConnectServerResult> _connectToSeverResultSubject;

    /// <summary>
    /// 서버 연결의 결과를 통지 한다.
    /// </summary>
    public IObservable<ConnectServerResult> ConnectToSeverObservable
    {
        get
        {
            if (_connectToSeverResultSubject == null)
                _connectToSeverResultSubject = new Subject<ConnectServerResult>();

            return _connectToSeverResultSubject.AsObservable().First(); // OnCompleted를 발행하기 위한 First 선언
        }
    }

    /// <summary>
    /// 서버에 접속 성공했다.
    /// </summary>
    public override void OnConnected()
    {
        base.OnConnected();

        _connectToSeverResultSubject?.OnNext(new ConnectServerSuccess());
    }

    /// <summary> 
    /// Photon 서버와 연결이 끊겼다.
    /// </summary> 
    /// <param name = 'cause'> 원인 </param> 
    public override void OnDisconnected(DisconnectCause cause)
    {
        base.OnDisconnected(cause);
        
        _connectToSeverResultSubject?.OnNext(new ConnectServerFail(cause));
    }
    #endregion
    
    #region Lobby
    /// <summary>
    /// 로비에 연결 한 것을 통지하는 Subject
    /// </summary>
    private Subject<Unit> _onJoinedLobby;

    /// <summary>
    /// 로비 입장 성공을 알린다.
    /// </summary>
    public IObservable<Unit> OnJoinedLobbyAsObservable
    {
        get
        {
            if (_onJoinedLobby == null)
                _onJoinedLobby = new Subject<Unit>();

            return _onJoinedLobby.First();
        }
    }

    /// <summary>
    /// 로비에 입장 한다.
    /// </summary>
    public override void OnJoinedLobby()
    {
        base.OnJoinedLobby();
        
        _onJoinedLobby?.OnNext(Unit.Default);
    }
    #endregion
}

using Photon.Realtime;

/// <summary> 
/// 서버 연결의 성공 여부 판정 클래스 
/// </summary> 
public abstract class ConnectServerResult { }

/// <summary> 
/// 성공 
/// </summary> 
public class ConnectServerSuccess : ConnectServerResult {  }

/// <summary> 
/// 실패 
/// </summary> 
public class ConnectServerFail : ConnectServerResult
{
    /// <summary> 
    /// 실패 원인 
    /// </summary> 
    public DisconnectCause Cause { get; }

    public ConnectServerFail(DisconnectCause cause) => Cause = cause;
}
```

수정 부분

- PhotonCloud v2 부터는 PhotonCloud의 콜백은 2종류. PhotonSingletonMonoBehaviourFast 스크립트 생성해서 포톤 전용 싱글톤을 만들었다. (SingletonMonoBehaviourFast 와 구조는 동일하고, MonoBehaviour 대신에 MonoBehaviourPunCallbacks를 상속 받는 구조)
- 기존 콜백 네이밍이 변경되어 해당 부분은 수정.
    - OnConnectedToPhoton → OnConnected
    - OnFailedToConnectToPhoton → OnDisconnected
    - OnJoinedLobby → OnJoinedLobby

## 코루틴과 StartAsObservable을 사용하여 로그인 처리

PhotonCallbacks가 준비되면 코루틴 내에서 처리를 진행하면 됩니다.

다음은 Button을 눌렀을 때 로그인을 시도하는 코드 입니다.

```cs
using System.Collections;
using Photon.Pun;
using UniRx;
using UnityEngine;
using UnityEngine.UI;

public class ConnectToPhotonServerSample : MonoBehaviour
{
    private PhotonCallbacks _callbacks;

    private void Start()
    {
        _callbacks = PhotonCallbacks.Instance;
        
        // 버튼이 눌려 졌을 때 로그인 프로세스 시작
        GetComponent<Button>()
            .OnClickAsObservable()
            .Subscribe(_ => StartCoroutine(LoginTask()));
    }

    /// <summary>
    /// Photon 로그인 처리를 한다.
    /// </summary>
    private IEnumerator LoginTask()
    {
        Debug.Log("서버에 연결 시작");
            
        // 연결 시작
        PhotonNetwork.GameVersion = "0.1";
        PhotonNetwork.ConnectUsingSettings();
        
        // 성공 여부 판정 저장 용
        ConnectServerResult serverResult = null;
        
        // 로그인 처리의 종료를 대기
        yield return _callbacks
            .ConnectToSeverObservable
            .StartAsCoroutine(x => serverResult = x);
        
        // if 문에서 성공 여부를 판정 할 수 있다
        if (serverResult is ConnectServerFail)
        {
            Debug.LogError("로그인 실패");
            Debug.LogError((ConnectServerFail) serverResult);
            // 결과가 실패하면 종료
            yield break;
        }
        
        // 로그인 성공 후 계속 진행
        Debug.Log("로그인 성공");
        
        // 로비 입실을 대기 한다
        yield return _callbacks
            .OnJoinedLobbyAsObservable
            .StartAsCoroutine();
        
        Debug.Log("로비 입실 완료");
        
        // 다음 로그인 후 처리를 진행 한다.
    }
}
```

## 정리

StartAsCoroutine을 사용함으로써 코루틴 중에서 Task의 await 처리를 비슷하게 사용할 수 있기 때문에 비동기 처리를 동기 처리 처럼 쓸 수 있었습니다.

로그인 메인 처리 부분은 동기 처리로 아주 읽기 쉽게 만들어졌지만, 대신 뒤죽박죽한 부분이 PhotonCallbacks 내부에 들어간 느낌 입니다.

아직 이러한 방법으로 구현을 시작한지 얼마 되지 않아 앞으로도 이러한 방법으로 계속 진행 할지는 모르겠지만, 잘 되면 다시 포스트로 정리하고 싶습니다.