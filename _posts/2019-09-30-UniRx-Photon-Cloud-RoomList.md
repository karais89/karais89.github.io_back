---
layout: post
title: "UniRx를 사용하여 Photon Cloud의 RoomList의 업데이트 모니터링"
excerpt: "UniRx를 사용하여 Photon Cloud의 RoomList의 업데이트 모니터링 번역"
date: 2019-09-30 22:40:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/228e42e6c65b0cbc349e](https://qiita.com/toRisouP/items/228e42e6c65b0cbc349e)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

Photon Cloud의 RoomList 업데이트를 UniRx를 이용해서 구현하도록 해보았습니다.

## 준비

- [[Unity, UniRx, Photon] PUN을 Rx 대응 해 보았다](https://naichilab.blogspot.com/2014/11/unityunirxphotonpunrx.html)
- [Unity에서 약간 빠른 싱글 톤 (Singleton)](http://tsubakit1.hateblo.jp/entry/20140709/1404915381)

위를 참고하여 Photon 콜백을 집계하고 받아들이는 PhotonCallbacks라는 싱글톤 오브젝트를 만들었습니다.

이 PhotonCallbacks은 Photon 콜백을 모두 Observable로 변환하여 제공 해줍니다.

## 방 업데이트 알림 Observable

```csharp
using System.Linq;
using UnityEngine;
using UniRx;

/// <summary>
/// Photon 방 정보 변경을 모니터링 한다.
/// </summary>
public class RoomListManager : TypedMonoBehaviour
{
    private IObservable<RoomInfo[]> _onRoomInfoChangedObservable;

    /// <summary>
    /// 방 정보가 업데이트되었을 때 새로운 방 목록을 제공하는 Observable
    /// </summary>
    public IObservable<RoomInfo[]> OnOnRoomInfoChangedObservable
    {
        get { return _onRoomInfoChangedObservable; }
    }

    public override void Awake()
    {
        base.Awake();

        // OnReceivedRoomListUpdate 타이밍에서 최신 RoomList []를 흐르는 스트림
        _onRoomInfoChangedObservable = PhotonCallbacks.Instance
            // OnReceivedRoomListUpdate이 실행되었을 때 발화 Observable
            .OnReceivedRoomListUpdateAsObservable()
            // 방 업데이트 알림이 온 타이밍에서 최신 RoomInfo [] 으로 대체
            .Select(_ => PhotonNetwork.GetRoomList())
            // Hot 변환 (현재 방 목록으로 초기화)
            .Publish(PhotonNetwork.GetRoomList())
            .RefCount();
}
```

Photon 버전 변경으로 인해 포스트에 있는 스크립트를 아래 처럼 변경 하였습니다.

```csharp
using System;
using System.Collections.Generic;
using Photon.Realtime;
using UniRx;
using UnityEngine;

public class RoomListManager : MonoBehaviour
{
    private IObservable<List<RoomInfo>> _onRoomInfoChangedObservable;

    /// <summary>
    /// 방 정보가 업데이트 되었을 때 새로운 방 목록을 제공하는 Observable
    /// </summary>
    public IObservable<List<RoomInfo>> OnRoomInfoChangedObservable => _onRoomInfoChangedObservable;

    private void Awake() => _onRoomInfoChangedObservable = PhotonCallbacks.Instance
        .OnRoomListUpdateAsObservable()
        .Publish()
        .RefCount();
}
```

수정한 부분

- UniRx의 TypedMonoBehaviour와 ObservableMonoBehaviour 퍼포먼스 이슈로 인해 Deprecated 되었다. 대신에 ObservableTriggers를 사용하면 된다.
- PhotonNetwork.GetRoomList() → OnRoomListUpdate 콜백으로 대체
- OnReceivedRoomListUpdate() → 없어짐 위의 OnRoomListUpdate로 통합.
- 수정한 스크립트의 경우 Select 해주는 부분이 없어서 사실 Hot 변환 자체가 필요 없지만, 기존 스크립트와 통일성을 유지하기 위해 Hot 변환해주는 부분을 그냥 유지 하였습니다. (PhotonCallbacks에서 Subject 값을 넘겨주기 때문에 Subject의 경우 자체적으로 Hot 입니다.)

UniRx의 Publish 해주는 부분은 아래와 같이 이해 했다. Hot 변환 현재 방 리스트로 초기화 해주는 부분인데.  방 정보가 갱신될때 ReceivedRoomListUpdate가 호출되고, 호출된 이후에는 GetRoomList로 룸 리스트를 받고 난 후. Cold한 Stream을 Hot으로 변환해주면서 GetRoomList로 초기화 해준다.

결국 위 스크립트는 **OnRoomInfoChangedObservable** 방 목록의 업데이트가 있을 때 최신의 방 정보를 흘려 주는 스트림입니다.

이 스트림을 Subscribe하면 방 정보가 업데이트 되었을 때 자동으로 최신 상태를 취득 할 수 있습니다.

## 사용법

OnRoomInfoChangedObservable를 Subscribe 하는게 전부 입니다.

OnReceivedRoomListUpdate 콜백이 온 타이밍에서 Subscribe의 처리가 실행 됩니다.

```csharp
using UniRx;
using UnityEngine;

public class RoomListView : MonoBehaviour
{
    [SerializeField] private RoomListManager _roomListManager;

    private void Start() =>
        // 방 정보가 업데이트 되었을 때 현재의 방의 개수를 콘솔에 출력한다.
        _roomListManager
            .OnRoomInfoChangedObservable
            .Subscribe(roomList =>
            {
                Debug.Log($"현재 방 수 : {roomList.Count}"); 
            });
}
```

이번에는 그냥 Debug.Log로 방의 개수를 출력하고 있는 처리 뿐입니다.

이 Subscribe의 내용으로 GUI를 변경하면 좋을 것 같습니다.

## 여담

Hot과 Cold 특성을 제대로 파악해 두지 않으면 의도하지 않은 동작을 하므로 주의하는 것이 좋습니다.

참고: [Rx의 Hot과 Cold에 대해](https://qiita.com/toRisouP/items/f6088963037bfda658d3)

## 추가 (2015/04/07)

ReactiveProperty이라는 것이 UniRx에 추가 되었기 때문에, 이 쪽을 사용하는 것이 더 간단하게 처리할 수 있습니다.

```csharp
using System.Collections.Generic;
using Photon.Pun;
using Photon.Realtime;
using UniRx;

public class PhotonRoomListModel : MonoBehaviourPunCallbacks
{
    public ReactiveProperty<List<RoomInfo>> RoomInfoReactiveProperty
        = new ReactiveProperty<List<RoomInfo>>();

    public override void OnRoomListUpdate(List<RoomInfo> roomList) =>
        RoomInfoReactiveProperty.Value = roomList;
}
```

```csharp
using UniRx;
using UnityEngine;

public class RoomsUpdateObserver : MonoBehaviour
{
    [SerializeField] private PhotonRoomListModel roomModel;
    
    private void Start() => roomModel
        .RoomInfoReactiveProperty
        .AsObservable()
        .Subscribe(rooms =>
        {
            Debug.Log(rooms.Count);
        });
}
```

PhotonRoomListModel 스크립트의 경우 원본 포스트의 스크립트와 내용이 다릅니다. 포톤 클라우드 버전이 달라지면서 API가 달라져서 새로운 스크립트로 대체 합니다. 아래는 원본 스크립트 입니다.

```csharp
public class PhotonRoomListModel : MonoBehaviour
{
    public ReactiveProperty<RoomInfo[]> RoomInfoReactiveProperty
        = new ReactiveProperty<RoomInfo[]>(new RoomInfo[0]);

    private void OnReceivedRoomListUpdate()
    {
        _reactiveRooms.Value = PhotonNetwork.GetRoomList();
    }
}
```