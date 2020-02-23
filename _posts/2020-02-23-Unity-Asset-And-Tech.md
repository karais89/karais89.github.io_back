---
layout: post
title: "Unity 개발에 편리했다 자산 서비스 소개 및 Unity에서의 프로그래밍 기법"
excerpt: "Unity 개발에 편리했다 자산 서비스 소개 및 Unity에서의 프로그래밍 기법 번역"
date: 2020-02-23 16:46:00 +0900
tags: [unity3d, unirx]
---
* TOC
{:toc}

## 환경

---

- macOS Catalina v10.15
- Unity 2019.2.10f1
- Github Desktop
- Rider 2019.2
- UniRx v7.1.0

원문 : [https://qiita.com/toRisouP/items/2a1d4185d7f54e0cca24](https://qiita.com/toRisouP/items/2a1d4185d7f54e0cca24)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

# 자기 소개

- 이름: とりすぷ( [@toRisouP](https://qiita.com/toRisouP) )
- 취미로 Unity를 이용해 게임 개발을 하고 있습니다.

# 지금 만들고 있는 게임

- 제목 :  [ハクレイフリーマーケット](https://www.mikoma.net/)
- 동방 2차 창작 게임
- 3D 대전 액션 게임
- **네트워크 대전 지원** (최대 6인)
- 사용자 관리 기능

아이템을 자기 진영에 넣으면 점수가 나는 게임 입니다.

![](/images/unity3d/2020-02-23-1.jpg)

# 이번 내용

- ハクレイフリーマーケット 개발을 통해 얻은 지식과 노하우를 정리합니다.
- 1인 개발에서 얻은 지식이므로, 팀 개발에 그대로 적용 할 수 없을지도 모릅니다
    - 프로그래머 관점에서의 내용 입니다.
- 생각나는대로 썻기 때문에 내용에 맥락은 없습니다.
    - 궁금하신 부분만 발췌해서 읽으세요.

# 정리 목록

크게 2가지가 있습니다.

- 추천 에셋 서비스 편
- 프로그래밍 편

# 추천 에셋 서비스 편

## 추천 에셋 서비스

- 어떤 기능이 필요하게 되었을 때 이용한 에셋이나 서비스를 소개 합니다.

## 1. 네트워크를 만들고 싶다

### 사용한 것 : Phton Cloud

### Photon

- 일본에서는 GMO 인터넷에서 제공하는 네트워크 엔진
- 서버 클라이언트 형태의 네트워크를 만들 수 있게 된다
- 그 중에서도 "Photon Cloud"는 서버를 클라우드로 제공 해준다.
    - Unity 용 SDK도 공개되어 있다 (PUN)
    - 20명 동시 연결까지는 무료

[공식 사이트](https://www.photonengine.com/ko-kr/Photon)

### Unity + Photon Cloud에서 할 수 있는 일

- 방을 만들고 참가자를 모집한다.
- Transform, Animator등 다른 임의의 정보를 네트워크를 통해 동기화 할 수 있다.
- 네트워크를 통해 메서드를 호출 해 실행할 수 있다.

네트워크 통신에 필요한 것은 대략 적으로 지원하고 있다.

### 그러나 네트워크를 쉽게 만들 수 있다고는 말하지 않았다!

### 네트워크 개발의 어려움

- **생각해야 될 상태가 늘어난다**
    - 같은 객체가 "로컬"과 "네트워크 너머 상대방의 세계(리모트)" 양쪽에 동시에 존재한다.
    - 로컬 및 원격 세계의 상태를 고려하여 객체를 조작하지 않으면 안된다
- **간단한 것도 비동기 처리 해야 된다**
    - 단지 "떨어진 아이템을 줍는다"라는 처리만으로도 매우 귀찮아진다.
    - 참고: [아이템 복사 방지](https://qiita.com/toRisouP/items/e56cf1c81144d49653bf)
- **디버깅이 매우 어렵다**
    - 통신시의 미묘한 시기에 발생하는 버그 라든지 재현이 어렵다.
- 통신 상대가 없으면 디버깅 및  레벨 디자인을 할 수 없다.
    - 동작 확인을 위해 여러 플레이어 필요
    - 실시간으로 사람을 모을 수 밖에 없다.

### 게임 개발 초보자에게는 네트워크 개발은 추천 할 수 없다

- Photon이 지원해주는 것은 어디 까지나 통신의 낮은 레이어 부분 만 지원해주고, 응용프로그램은 결국 자신이 구현해야 한다.
- 오프라인 게임에 비해 네트워크 개발은 구현하지 않으면 안되는 것이 몇 배는 있다
- 어느정도 개발 경험을 쌓고 프로그래밍에 자신감이 붙은 사람이 아니면 손을 대는 것은 위험하다고 생각한다.

## 2. 사용자 관리가 하고 싶다

### 사용한 것: 니프티 클라우드 mobile backend (NCMB)

### NCMB

- 니프트에서 제공하는 mBaaS 서비스
    - (mBaaS : Mobile Backend as a Service)
- 회원 가입 기능, 데이터 저장, 푸시 알림 등의 서비스를 이용할 수 있다.
- 200만 API/월 요청까지라면 무료로 이용할 수 있다.

[공식 사이트](https://mbaas.nifcloud.com/)

[역주]

비슷한 서비스로 [PlayFab](https://playfab.com/) 이나 [Firebase](https://firebase.google.com/?hl=ko)도 제공하고 있다.

한국에서는 이 2개의 서비스로 시작하는게 더 나을 것 같다.

### NCMB에서 사용자 관리

- NCMB를 사용하면 간단하게 게임에 회원 가입 및 로그인 기능을 구현할 수 있다.
- 이메일 주소 인증에도 대응 된다.
- 자기 부담으로 계정 관리 서버를 만들지 않아서 정말 고맙다.

## 3. 제품 정품 인증 기능을 원한다.

### 사용한 것 : 없다

### 왜?

- 일련 번호를 가진 사람만 즐길 수 있는 기능을 원한다.
    - 실제 구입한 사람만 즐길 수 있게 하고 싶다
- **시리얼 정품 인증을 제공 해주는것은 좋은 느낌의 서비스는 존재하지 않는다.**

### 결국 어떻게 했는지?

- 자기 부담으로 인증 서버를 만들 수 밖에 없었다.
    - Ruby on Rails로 빠르게 Web 서비스를 만들 수 잇어서 좋았다.
- 자기 부담 서버와 NCMB를 연계시켜 구입자, 미 구매자 관리를 했따.

자세한 내용은 [Unity와 NCMB에서 사용자 관리를 구현 해본 이야기](https://www.slideshare.net/torisoup/unityncmb-148151719)를 참조

## 4. NPC 상대를 만들고 싶다

### 사용한 것

[Behaviour Designer](https://assetstore.unity.com/packages/tools/visual-scripting/behavior-designer-behavior-trees-for-everyone-15277)

![](/images/unity3d/2020-02-23-2.png)

### Behaviour Designer

- BehaviourTree를 GUI에서 만들 수 있는 에셋
- NPC의 행동을 최소 단위로 분할하고 그들을 결합하여 사람 같이 움직이게 할 수 있다.
- 상태 머신을 짜는 것에 비해 매우 알기 쉽게 NPC를 만들 수 있으므로 추천한다.

### 완성된 실제 Tree

![](/images/unity3d/2020-02-23-3.jpeg)

- 만드는데 걸린 시간은 15 시간 정도
- 70% 정도는 자작 Task 이다.

### 실제로 만든 NPPC의 동영상

![](/images/unity3d/2020-02-23-4.jpeg)

[https://www.youtube.com/watch?v=nLsR3nrmppQ](https://www.youtube.com/watch?v=nLsR3nrmppQ)

## 5. 게임 패드의 관리를 하고 싶다.

### 사용한 것

[Rewired](https://assetstore.unity.com/packages/tools/utilities/rewired-21676)

![](/images/unity3d/2020-02-23-5.png)

### Rewired

- 게임 패드의 관리에 특화된 에셋
- 게임 패드의 자동 검출및 자동 할당을 동적으로 해준다.
- 게임패드의 형태에 따라 적절하게 변경해준다.
- **방대한 종류의 게임 패드가 처음부터 등록되어 있다.**
    - 일단 수중에 있던 게임 패드는 대략 대응 된다.

[역주]

Unity에서 새로 발표한 [new Input System](https://blogs.unity3d.com/kr/2019/10/14/introducing-the-new-input-system/)이 기능을 대체할 수 있을지 확인해봐야 될 것 같다.

## 6. 물리 기반 CharacterController을 원한다.

### 사용한 것

[Easy Character Movement](https://assetstore.unity.com/packages/templates/systems/easy-character-movement-57985)

![](/images/unity3d/2020-02-23-6.png)

### Easy character Movement

- 물리 연산과 상호 간섭하면서 작동하는 캐릭터를 쉽게 만들 수 있는 에셋
- CharacterController과 같은 인터페이스에서 RigidBody를 취급 할 수 있다.
    - Move() 메서드로 이동하면서 AppleForce()로 날려 버린다 같은 것을 할 수 있다.
    - 움직이는 바닥의 영향을 받게 하고 싶다.

## 7. 개체를 눈에 띄게 하고 싶다

### 사용한 것

[Highlighting System](https://assetstore.unity.com/packages/tools/particles-effects/highlighting-system-41508)

![](/images/unity3d/2020-02-23-7.png)

### Highlighting System

- 스크립트를 연결하면 GameObject에 윤곽선을 달 수 있다.
    - 특별한 쉐이더를 사용하지 않고 스크립트만으로 가능하다.
- 점멸 기능과 벽을 투명하게 그리는 기능도 있다.
- 플레이어에 윤곽을 붙여두면 시인성이 향상되므로 추천 한다.

# 추천 자산 서비스 편 완료

# 프로그래밍 편

## 프로그래밍 편

- Unity에서 프로그램을 해오면서 발생한 문제와 그 해결책을 소개 한다.

1. 형 안전이 아닌 곳에서 죽는 문제
2. 매니저 싱글톤의 배치 문제
3. GOD 클래스가 있는 문제
4. 컴포넌트간의 연계 방법
5. View와 Model의 연계

## 1. 타입 안전이 아닌 곳에서 죽는 문제

"시작은 하는데 왠지 동작이 이상하다. 움직이지 않는다."

"컴파일 에러는 안났지만, 움직일때 에러가 난다"

### 타입 안전은?

- **프로그램 작성을 잘못 했을 때 제대로 컴파일 오류가 발생한 상태**
    - "타입"에 의해 프로그램의 정당성이 담보된 상태의 수
    - 타입 안전성이 없는 경우, 어딘가 기술을 잘못해도 인간이 그 실수를 알아차릴 방법이 없다.
    - 버그가 발생했을 때, 수상한 곳을 중단점으로 코드를 체크하게 된다.

Unity에서 타입 안전이 아닌 기능

- SendMessage
- Tag
- Scene 전환
- Invoke
- Animator 플래그 지정

대부분 문자열 기반으로 처리를 하고 있는 곳

### 예: 태그

```cs
public void OnCollisionEnter(Collision collision)
{
    // Enemy의 철자가 틀렸지만 컴파일 에러는 되지 않는다!
    if (collision.gameObject.tag == "Eneny")
    {
        // 처리 부분
    }
}
```

### 예: Animator 애니메이션 플래그
```cs
// IsRunning을 철자가 틀렸지만, 컴파일 에러는 되지 않는다!
animator.SetBool("IsRuning", true);
```

### 대책

### 문자열 기반의 처리를 없앤다

### 타입 안전하게 쓰는 방법

- 문자열을 사용하여 동작을 제어하고 있는 것이 악의 근원이다.
- 문자열을 사용 장소를 제한하고 가능한 "타입"의 형태 모양으로 변경하면 된다.
    - enum을 사용 하든지, 인터페이스를 사용 하든지, 프로퍼티로 감싸던지

### 예 1

- Tag 비교에 enum을 사용

```cs
enum EntityType
{
    Player,
    Enemy,
    Boss
}

public void OnCollisionEnter(Collision collision)
{
    // enum의 ToString은 느리지만..
    if (collision.gameObject.tag == EntityType.Enemy.ToString())
    {

    }
}
```

### 예 2

- 원래 Tag를 사용하지 않고 타입으로 비교
```cs
public void OnCollisionEnter(Collision collision)
{
    // IEnemy 인터페이스를 구현 한 컴포넌트를 가지고 있는지 알아
    var enemyComponent = collision.gameObject.GetComponent<IEnemy>();
    if (enemyComponent != null)
    {
        // 처리
    }
}
```

### 예 3

- 애니메이션 플래그를 프로퍼티로 감싼다.

```cs
Animator animator;

/// <summary>
/// 이동 애니메이션 플래그
/// </summary>
private bool IsRunning
{
    // "IsRunning"라는 문자열이 등장하는 것은 이곳이 유일하다.
    // (여기 만 오탈자가 발생하지 않도록 주의 해두면 된다)
    set { animator.SetBool("IsRunning", value); }
}

void Start()
{
    animator = GetComponent<Animator>();

    // bool을 대입하는 것만으로 사용할 수 있다.
    IsRunning = true;
}
```

### "타입 안전이 아닌 곳에서 죽는 문제" 요약

- int 형과 string 타입을 사용하여 조건 판전을 실시하고 있는 곳은 실수가 발생하기 쉽다.
- 인터페이스를 만들거나, enum을 사용하거나, 프로퍼티로 감싸던지 어쨌든 원시적인 형태가 노출되지 않도록 유의하면 된다.

## 2. 매니저 싱글톤의 배치 문제

"Editor 상에서 씬을 지정해서 실행하면 에러가 발생한다."

"같은 게임 오브젝트가 2개가 존재한다"

### 매니저 싱글톤

- 씬을 횡당하여 리소스 등을 계속 관리하는 싱글톤
    - 장면 전환시 전환 애니메이션 관리
    - 사운드 관리
    - 게임의 설정 항목 관리
- 항상 1개 이며, 게임의 실행 중에 존재하지 않으면 게임이 올바르게 동작하지 않는다.

### 매니저 싱글톤은 어떻게 초기화 하나?

- 씬에 Prefab화한 싱글톤을 배치하는 것 만으로 괜찮지 않나?
    - `DontDestroyOnLoad`로 지정하고 한 번 배치하면 사라지지 않도록 한다.

### 안됩니다.

### 씬에 처음부터 싱글톤을 놓아 두면 안되는 이유

- **싱글톤을 배치하고 있지 않는 씬에서 게임을 실행하면 싱글톤이 존재하지 않으면 죽는다.**
    - Editor에서 디버깅 중에 발생한다.
    - 전체 씬에 싱글톤을 배치하는 것 같은 것은 하고 싶지 않다.
- **싱글톤이 존재하는 씬을 방문할때마다 하나씩 생성되어 증식된다.**
    - 다중 생성되면 지우는 것도 근본적인 해결책이 되지 않는다.

### 대책

### "필요한 타이밍에서 처음으로 싱글톤을 동적으로 생성하면 된다"

필요할때

- 이미 싱글톤이 존재한다면 그것을 사용한다.
- 없다면 새로 생성 한다.

이것만으로 해결 할 수 있다 (즉 단순한 지연 초기화)

### 구현 예
```cs
using UnityEngine;

/// <summary>
/// 씬 전환을 실시하는 클래스
/// </summary>
public static class SceneLoader
{
    /// <summary>
    /// 매니저 싱글톤의  Prefab 경로
    /// </summary>
    private static readonly string managerPrefabPath = "Managers/TransitionManager";

    /// <summary>
    /// 싱글턴 전환 애니메이션 제어 구성 요소
    /// </summary>
    private static TransitionManager _transitionManager;

    /// <summary>
    /// 이미 싱글 톤이 존재한다면 그것을 돌려주고, 없는 경우 만든다.
    /// </summary>
    private static TransitionManager TransitionManager
    {
        get
        {
            if (_transitionManager != null) return _transitionManager;
            if (TransitionManager.Instance == null)
            {
                var resource = Resources.Load(managerPrefabPath);
                Object.Instantiate(resource);
            }
            _transitionManager = TransitionManager.Instance;
            return _transitionManager;
        }
    }

    /// <summary>
    /// 씬 전환을 시작
    /// </summary>
    /// <param name="scene"> 다음 씬 </param>
    public static void LoadScene(GameScenes scene)
    {
        TransitionManager.StartTransaction(scene);
    }
}
```

static 클래스에 매니저 싱글톤의 존재 확인을 끼워 없으면 생성시킨다.

### 구현 예
```cs
SceneLoader.LoadScene(GameScenes.Title);
```

호출자는 싱글톤을 의식하지 않고 static 클래스에 구현된 메소드를 실행 한다.

### 매니저 싱글톤의 배치 문제 요약

- **지연 초기화를 이용하면 해결 할 수 있다.**
- **그러나 처음 액세스 할 때 싱글톤의 생산 비용이 발생하는 단점이 있다.**
    - 스탠드얼론일때는 기동 직후 장면에서 취합하여 초기화를 실행한다.
    - 에디터 실행시에만 이 지연 초기화를 사용한다.
    - 라고 하면 좋을지도 모르겠다.

## 3. GOD 클래스가 있는 문제

"이 클래스는 1000 라인이 넘어 간다."

"필드 변수와 메소드가 많이 있어 어떻게 의존하고 있는지 모르겠다!"

"Update()의 내용이 위험하다!"

[역주]

1000 라인이 넘어간다고 무조건 GOD 클래스는 아니다.

### GOD 클래스?

- 여러가지 기능이 담겨있어 거대한 클래스
- 피해야 할 안티 패턴
- 대충 만들다 보면 이렇게 된다.
    - Unity의 공식 튜토리얼의 프로젝트를 그대로 확장 해 나가다보면 GOD 클래스가 되기 쉽다.

### GOD 클래스인 PlayerController

- Input 관리, 이동, 공격, 데미지, 애니메이션, 음향 효과, 입자 효과, UI 관리 등..
- 이러한 처리가 **모두 하나의 클래스**에 정의되어 있다.
    - 상태를 나타내는 플래그가 난무하고 Update() 안에서도 이것저것 구현 되어 있다.

이렇게 만드는 사람들이 많을 것이다.

### 대책

### 컴포넌트 분할

![](/images/unity3d/2020-02-23-8.jpg)

### 컴포넌트 분할

- **단일 책임 원칙**을 의식하고 구성 요소를 분할한다.
    - 컴포넌트 1개는 1개의 역할만 담당한다.
    - 이동 처리는 PlayerMover 에서, 애니메이션 관리는 PlayerAnimation 같이 처리 한다.
- 1개의 컴포넌트가 다루는 영역이 좁아지기 때문에 필요한 최소한의 필드와 메서드만 구현되서 코드의 품질이 좋아진다.

### 컴포넌트를 분할 할 때 조언

- **그 컴포넌트가 제공하는 "기능"에 관계있는 처리만을 구현**
    - "이동"에 특화된 컴포넌트는 이동 처리에 필요한 것만 제공한다.
    - 자신의 책임 이외의 처리는 다른 컴포넌트에 위양한다.

### 컴포넌트 분할의 장점

- **1개의 컴포넌트가 맡는 책임이 명확해진다.**
    - 불필요한 일을 생각하지 않고 지금 컴포넌트가 할 일만 생각하고 구현하면 된다.
- **어떤 처리가 어떤 컴포넌트에 쓰여져 있는지 알기 쉽게 된다.**
    - 클래스의 이름으로 처리 위치를 찾을 수 있게 된다.

### 컴포넌트 분할의 단점

- 방대한 수의 컴포넌트를 연결해야 하며, 그 관리가 힘들어진다.
    - 악마 같은 RequireComponent를 쓰는 것도 고충이다.
    - MonoBehaviour을 상속하지 않는 Pure한 클래스를 생성하고 그 쪽에 처리를 위양하는 방법도 있다.
- **Update 호출 비용이 증가 한다.**
    - (UniRx의 `ObservableUpdateTrigger`를 사용하면 Update 비용은 일단 줄일 수 있다.)
- 뒤에서 다룰 "종속성"과 "클래스 간의 연계" 문제가 나온다.

### 하나님 클래스는 절대 악인가?

- **규모가 작고 감당할 수 있는 범위라면** 모든 처리를 하나의 클래스에 담아도 된다.
- 처음에는 일단 프로그래밍 하고, 코드에 악취가 날때 분할 하는 방식도 괜찮다.

## 3. 의존, 참조 관계가 복잡해지는 문제

"어떤 컴포넌트가 어디에 의존하는 거야?"

"컴포넌트를 재사용 하고 싶지만, 어쩐지 여러곳에 의존하고 있어 사용할 수 없다!"

"어? 상호 참조하고 있어 초기화에 실패 하고 있다."

### 종속성이 복잡해지는 문제

- 분할된 컴포넌트를 계속 연결해 나가면 복잡해진다.
- 어디를 만지면 어디에 영향을 미칠지 예측 불가능 해진다.

### 대책

### 구현하기 전에 클래스 다이어그램을 작성하고 설계한다.

### 설계를 제대로 하자

- 복잡해지는 원인은 대부분 닥치는 대로 구현하기 때문이다.
    - 사양이 원래 복잡한 경우는 어쩔 수 없다.
- 설계 클래스 다이어그램을 만들어두면 구현 작업을 분담 할 수 있게 된다.
    - 누가 구현해도 설계대로 만들어질 것이다.
- 잡다한 클래스 다이어그램도 적어 놓으면 나중에 전체 형태를 파악이 가능하게 된다.

### 클래스 다이어그램을 작성하는 추천 도구

- [PlantUML](https://qiita.com/nakahashi/items/3d88655f055ca6a2617c)을 사용하면 쉽게 그림이 그려지므로 추천 한다.
    - 텍스트를 쓰는 것만으로 자동으로 클래스 다이어그램이 생성된다.

[역주]

Visual studio Code에서도 똑같은 확장 프로그램을 설치하여 실행 할 수 있다. 클래스 다아어그램을 문자로 관리를 하면 Git으로 버전관리도 되고, 협업도 된다.

- [PlantUML 공식 홈페이지](http://plantuml.com/ko/)

![](/images/unity3d/2020-02-23-9.gif)

### 실제로 작성한 클래스 다이어그램 예

![](/images/unity3d/2020-02-23-10.jpg)

### 클래스 설계시 조언

- **상호 참조, 순환 참조를 가능한 피하자**
    - 상호 참조, 순환 참조는 장점보다 단점이 더 눈에 띄기 때문에 안이하게 이용하지 말자
    - 닫힌 개념에서 국소적으로 사용할 정도로 억제해두면 좋다.
- "[추상화](https://qiita.com/tutinoco/items/6952b01e5fc38914ec4e)"와 "[의존관계역전](https://qiita.com/UWControl/items/98671f53120ae47ff93a#%E4%BE%9D%E5%AD%98%E9%96%A2%E4%BF%82%E9%80%86%E8%BB%A2%E3%81%AE%E5%8E%9F%E5%89%87)"을 이용하여 의존 관계를 정리하자.

![](/images/unity3d/2020-02-23-11.png)

### 의존관계역전

![](/images/unity3d/2020-02-23-12.png)

### 설계 노하우의 덩어리

- SOLID 원칙은 확실히 알고 있으면 좋다.
- "[코드스멜](https://ko.wikipedia.org/wiki/%EC%BD%94%EB%93%9C_%EC%8A%A4%EB%A9%9C)"을 느낄 수 있게 되면 좋다.
    - 익숙해지면 어디를 추상화 해야 되는지 감각적으로 알게 된다.
- 우선 여러 가지를 만들어 보고 어려움을 겪으면서, 거기에서 설계의 중요성을 실감하면 좋다.

### 이런 반론도 있었습니다.

"사양이 자주 바뀌기 때문에 설계를 못 해먹겠다!"

### "사양이 자주 변하니까 설계하지 않는다"는 올바른가?

- 솔직히 [케바케](https://namu.wiki/w/Case%20by%20case)라고 할 수 밖에 없다.
- 변경에 유연하게 만드는 것도, YAGNI로 만드는 것도 있다.
    - (YAGNI:  "You Ain't Gonna Need It". "필요한 작업만 해라", 사양 변경으로 유연하게 작업한 코드를 작성해 놓으면 코드가 불필요하게 장황해진다. 그러니 당장 필요한 작업에 집중하고 쓸데없는 작업은 하지 말라라는 개발 원칙) ~~Simple is Best~~
- **프로젝트에 따라 최적의 개발 스타일이 변화하기** 때문에 설계를 안하는 것이 효율이 좋다면  이렇게 할 것이다.
    - 설계하는 것이 나중에 편해지는 경우가 더 많지만, "아무래도 설계하고 싶지 않다"라는 사람에게는 강요할 수 없다.

### 덧붙여서

- 게임 잼도 제대로 설계하고 개발하니 잘 되었다.
- [글로벌 게임 잼에서 클래스 설계를 해서 원할하게 개발이 진행된 이야기](https://qiita.com/toRisouP/items/824aff814849ae41efe7)

### '의존, 참고 관계가 복잡해지는 문제' 요약

- 설계를 잘하는게 답
- 클래스 다이어그램을 만들어두면 일단 구현헤서 해메지는 않을 것이다.

## 4. 컴포넌트간의 연계 방법

**조각 조각낸 컴포넌트를 어떻게 조화시켜 동작시킬수 있을까?**

- "플레이어가 기절 하면, 이동하지 못하게 하고 기절 애니메이션을 재생하고 효과음을 재생하고 싶다"
- 상태 관리를 일원화해서 그곳의 변화를 감지해서 마음대로 처리가 움직이는 형태로 하고 싶다.

### 컴포넌트간의 연계

- 뭔가 있을 때마다 **이벤트를 발행**하는 것이 가장 편하다.
    - (**Observer 패턴**을 적용하여 연계시킨다)
- 각 컴포넌트는 "OO가 일어나면 XX를 한다"는 것만을 의식하는 형태로 작성한다.

### Observer 패턴?

### Observer 패턴

- 무엇인가 잘못되었을 때 감시 대상 측에서 이벤트를 날려주고 구독 측에서 처리하는 디자인 패턴
- 의존 관계를 역전 할 수 있는 장점이 있다
    - (매 프레임 상태의 플래그를 감시 한다 같은 구현을 없앨 수 있다)

### 모르면 일단 "[UniRx](https://assetstore.unity.com/packages/tools/integration/unirx-reactive-extensions-for-unity-17276)"를 사용하면 OK

### UniRx

- Reactive Extensions for Unity
- 리액티브 프로그래밍을 Unity에서 사용할 수 있다
- 할 수 있는 것이 너무 많아서 한마디로 설명 할 수 없다!
    - 과거에 정리한 포스트에 일부 설명하고 있기 때문에 그쪽을 참고하십시오.
    - [정리 링크](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

### UniRx의 ReactiveProperty<T>

- 이벤트 발행 기능을 가진 변수
- 값이 바뀌면 통지가 날라온다.
- 이것을 부모가 되는 클래스에 갖게 하고 자식이 이벤트를 기다리면 OK

### 구현 예

- "플레이어가 기절 하면 이동할 수 없게 하고 기절 애니메이션을 재생 시키고 싶다"
- UniRx를 이용한 구현 예를 소개한다.

```cs
using System.Collections;
using UniRx;
using UnityEngine;

public class PlayerHealth : MonoBehaviour
{
    /// <summary>
    /// 기절 플래그
    /// </summary>
    public BoolReactiveProperty IsStunned = new BoolReactiveProperty();

    /// <summary>
    /// 데미지 처리
    /// </summary>
    public void ApplyDamage()
    {
        // 기절하지 않았다면 기절 시킨다.
        if (!IsStunned.Value) StartCoroutine(StunCoroutine());
    }

    /// <summary>
    /// 기절하는 동안 수행되는 코루틴
    /// </summary>
    private IEnumerator StunCoroutine()
    {
        // 기절 플래그 ON
        IsStunned.Value = true;
        // 적당히 대기 한다.
        yield return new WaitForSeconds(5);
        // 기절 플래그 OFF
        IsStunned.Value = false;
    }
}

using UniRx;
using UnityEngine;

/// <summary>
/// 이동 관리 컴포넌트
/// </summary>
public class PlayerMove : MonoBehaviour
{
    /// <summary>
    /// 이동 허가 플래그
    /// </summary>
    public bool _canMove;
    
    private void Start()
    {
        // 참조의 취득은 원하는 방식으로 구현
        var playerHealth = GetComponent<PlayerHealth>();
        
        // 기절 플래그가 변경되면 이동 허가 플래그에 반영한다.
        playerHealth.IsStunned.Subscribe(x => _canMove = x);
        
        // 다음 부분은 _canMove 플래그를 사용하여 이동 처리를 작성하는 로직
        // 이하 생략
    }
}

using UniRx;
using UnityEngine;

public class PlayerAnimation : MonoBehaviour
{
    private Animator _animator;

    private bool IsStunned
    {
        set => _animator.SetBool("IsStunned", value);
    }
    
    private void Start()
    {
        _animator = GetComponent<Animator>();

        var playerHealth = GetComponent<PlayerHealth>();
        
        // 기절 플래그를 써 고쳐하면 Animator의 기절 플래그에 반영
        playerHealth.IsStunned.Subscribe(x => IsStunned = x);
    }
}

using UniRx;
using UnityEngine;

public class PlayerSound : MonoBehaviour
{
    private void Start()
    {
        var playerHealth = GetComponent<PlayerHealth>();
        
        // 기절 상태에 맞추어 효과음을 재생, 중지
        playerHealth.IsStunned.Subscribe(x =>
        {
            if (x)
            {
                Play();
            }
            else
            {
                Stop();
            }
        });

    }

    private void Play()
    {
        // 생략
    }
    
    private void Stop()
    {
        // 생략
    }
}
```

### "컴포넌트 연계 방법" 정리

- 이벤트 기반 구현을 하는 것이 방법
    - Observer 패턴을 사용할 수 있다.
- UniRx와 너무 잘 어울린다
    - 바로 리액티브 프로그래밍!

## 5. View와 Model의 연계

"어쩐지 Model (데이터 실체)과 View(UI)가 상호 의존적이다."

![](/images/unity3d/2020-02-23-13.png)

### View와 Model이 상호 참조하면 여러가지 위험이 있다.

- **데이터의 실체를 가지는 컴포넌트(Model)가 UI의 제어까지 해야 된다.**
    - Model이 GOD 클래스화 된다.
- **UI의 교체가 어렵다.**
    - Model이 View에 커플링이 되어 있기 때문에 쉽게 분리, 교체 할 수 없다.
- **UI를 재사용 할 수 없게 된다.**
    - View에 필요한 로직이 Model에 작성되어 있기 때문에, 다른 Model에 같은 View를 적용하려고하면 Model의 구현마다 다시 작성해야 한다.

### View와 Model을 잘 다루는 방법

- 옛날부터 계속 논의되어 온 문제
- 지금까지 MVC, MVP, MVVM 같은 아키텍쳐 패턴이 고안되어 왔다.
- **Unity에서 기분좋게 사용할 수 있는 패턴은 없는 걸까?**

### 있습니다.

### MV(R)P 패턴

### MV(R)P 패턴

- Model-View-(Reactrive)-Presenter 패턴
- UniRx의 제작자 neuecc씨가 고안한 UniRx를 이용한 Unity의 UI 아키텍쳐 패턴
- "Presenter"을 준비하고 Model과 View의 사이를 중개한다.
- **UniRx의 ReactiveProperty를 사용하는 것이 쉽다**

참고 : [UniRx 4.8 - 경량 이벤트 훅과 uGUI의 이벤트에 의한 데이터 바인딩](http://neue.cc/2015/04/13_510.html)

### MV(R)P 패턴 적용 후

![](/images/unity3d/2020-02-23-14.png)

[역주]

MV(R)P 설계에서는 Presenter가 Model과 View을 소지하고있다. 따라서 Presenter는 Model과 View를 알고 있는데, 상호 참조를 피하기 위해 Model과 View는 Presenter를 모르는 구조로 되어 있다.

안드로이드의 MVP 예제들에서는 보통 View와 Presenter가 1:1로 서로 알고 있는 구조로 되어 있다. 두개의 공통점은 Model과 View는 서로 모른다는 점이다.

MV(R)P의 경우 MVP에서 변형된 아키텍쳐 패턴으로 유니티에 조금 더 특화된 패턴이라고 생각하면 될 것 같다. 실제로 Unity에서 UniRx를 사용하여 MV(R)P 아키텍쳐를 사용하능 방법에도 여러가지 방법으로 사용하기도 한다.

아래는 MVP 패턴에 대한 추가 링크

- [Web 출신의 Unity 엔지니어의 대규모 게임의 기초 설계](https://developers.cyberagent.co.jp/blog/archives/4262/)
- [[Unity] MVP 및 MV(R)P의 구현 예와 여러가지 구현 방법의 고찰](http://light11.hatenadiary.com/entry/2019/01/23/231828)

### MV(R)P이 구현 예

- uGUI의 InputField의 입력을 Model에 저장한다.
- Model을 유지하는 데이터에 변경이 있는 경우 InputField도 업데이트 한다.
- InputField = View 자체로 취급 한다.

### Model
```cs
using UniRx;
using UnityEngine;

public class Model : MonoBehaviour
{
    // 외부에 공개하는 데이터
    public ReactiveProperty<string> Name = new ReactiveProperty<string>();
}
```

### Presenter
```cs
using UniRx;
using UnityEngine;
using UnityEngine.UI;

/// <summary>
/// Model과 View를 연결하는 Presenter
/// </summary>
public class Presenter : MonoBehaviour
{
    // view
    [SerializeField]
    private InputField _nameInputField;

    // model
    [SerializeField]
    private Model _model;
    
    private void Start()
    {
        // view가 업데이트되면 Model의 데이터를 업데이트 한다
        _nameInputField
            .OnValueChangedAsObservable()
            .Subscribe(x => _model.Name.Value = x);

        // model이 업데이트 되면 view를 다시 업데이트 한다.
        _model.Name
            .Subscribe(x => _nameInputField.text = x);
    }
}
```

### MV(R)P 패턴은 추천 한다.

- uGUI를 사용한다면 이 패턴을 사용하면 굉장히 편리하다.
- View를 위해 약간의 로직이 필요하다면 그것을 Presenter에 써도 좋다.
    - 데이터 형식의 반환 같은 것
- Presenter의 역할은 "Model과 View를 연결한다" 이므로, 거기에 벗어나는 처리는 하지 않는다.
    - Presenter의 구현은 몇 줄 ~ 수십 줄 정도로 끝날 정도가 적당하다.

[역주]

어떻게 프로그래밍을 하느냐에 따라 달라질 것 같다. 

실제 모델에서 비즈니스 로직 처리를 전부 완료하고, 값의 반환을 Presenter에 전달하는 방식으로 구현하거나, View 자체의 논리적인 변화가 너무 많아진다면 View를 위한 컴포넌트를 따로 만들어서 처리하는 방식등을 사용하여 구현해야 할 수 있다.

## 프로그래밍 편 완료

# 마지막으로

- 떠오른 생각을 닥치는 대로 썻는데, 분량이 대단히 많아 졌다.
- 조금이라도 도움이 되는 부분이 있으면 좋겠다.
- 이제 두번 다시 네트워크 게임은 만들고 싶지 않다.

감사합니다.