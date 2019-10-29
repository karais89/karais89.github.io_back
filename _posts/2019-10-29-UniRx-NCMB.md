---
layout: post
title: "[NCMB]회원가입/로그인 UniRx로 처리"
excerpt: "[NCMB]회원가입/로그인 UniRx로 처리 번역"
date: 2019-10-29 22:38:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/e101cb50835285b0fe6c](https://qiita.com/toRisouP/items/e101cb50835285b0fe6c)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

UniRx에 대한 기사 요약은 [여기](https://qiita.com/toRisouP/items/48b9fa25df64d3c6a392)

---

nifty의 [mBaaS](https://en.wikipedia.org/wiki/Mobile_backend_as_a_service)인 니프티 클라우드 [mobile backend (NCMB)](https://mbaas.nifcloud.com) 로그인  주변의 처리를 Observable로 변환해 UniRx에서 사용할 수 있도록 해 보았습니다.

[역주]

일본에서 제공하는 모바일 백엔드 서비스 중에 하나인 것 같다. 관련 API를 UniRx를 사용하여 Observable 화 시켜 사용하기 편하도록 코드를 작성한 것을 소개한다. 실제 동작은 테스트 하지 못하고, 원문 그대로 소스를 가져왔고, 코드를 한번 훑어 보는 용도를 위해 작성 하였습니다.

## IObservable 로 변형한 코드

static class로 정의하고 있습니다. 이용시에 using을 선언하십시오. (using ObservableNcmb;)

사용하기 쉽게 회원 등록시에 필드도 갱신 할 수 있는 기능을 추가 했습니다.
```cs
using System.Collections.Generic;
using NCMB;
using UniRx;

namespace ObservableNcmb
{
    public static class ObservableNcmbUserAuth
    {
        /// <summary>
        /// ID와 비밀번호로 로그인을 실행
        /// </summary>
        public static IObservable<NCMBUser> LoginAsync(string id, string password)
        {
            return Observable.Create<NCMBUser>(observer =>
            {
                NCMBUser.LogInAsync(id, password, e =>
                {
                    if (e == null)
                    {
                        observer.OnNext(NCMBUser.CurrentUser);
                        observer.OnCompleted();
                    }
                    else
                    {
                        observer.OnError(e);
                    }
                });
                return Disposable.Create(() => {; });
            });
        }

        /// <summary>
        /// ID와 비밀번호 회원 가입을 실행한다.
        /// </summary>
        /// <param name="id">ID</param>
        /// <param name="password">비밀번호</param>
        /// <param name="optionalData">기타필드</param>
        /// <returns></returns>
        public static IObservable<NCMBUser> SingUpAsync(
            string id,
            string password,
            Dictionary<string, object> optionalData = null)
        {
            return SingUpAsync(id: id, password: password, optionalData: optionalData, mail: null);
        }


        /// <summary>
        /// ID 및 이메일 주소와 비밀번호 회원 가입을 실행한다.
        /// </summary>
        /// <param name="mail">이메일주소</param>
        /// <param name="id">ID</param>
        /// <param name="password">비밀번호</param>
        /// <param name="optionalData">기타필드</param>
        /// <returns></returns>
        public static IObservable<NCMBUser> SingUpAsync(
            string mail,
            string id,
            string password,
            Dictionary<string, object> optionalData = null)
        {
            return Observable.Create<NCMBUser>(observer =>
            {
                var user = new NCMBUser
                {
                    Email = mail,
                    UserName = id,
                    Password = password
                };

                if (optionalData != null)
                {
                    foreach (var opt in optionalData)
                    {
                        user[opt.Key] = opt.Value;
                    }
                }

                user.SignUpAsync(e =>
                {
                    if (e == null)
                    {
                        observer.OnNext(user);
                        observer.OnCompleted();
                    }
                    else
                    {
                        observer.OnError(e);
                    }
                });
                return Disposable.Create(() => {; });
            });
        }

        /// <summary>
        /// 로그 아웃을 한다
        /// </summary>
        public static IObservable<Unit> LogoutAsync()
        {
            return Observable.Create<Unit>(observer =>
            {
                NCMBUser.LogOutAsync(e =>
                {
                    if (e == null)
                    {
                        observer.OnNext(Unit.Default);
                        observer.OnCompleted();
                    }
                    else
                    {
                        observer.OnError(e);
                    }
                });
                return Disposable.Create(() => {; });
            });
        }
    }
}
```

[역주]

`Observable.Create` 메서드를 이용하여, 각 API 별로 새로운 스트림을 정의 하여, UniRx로 변형 하고 있다. OnNext, OnCompleted, OnError등을 사용하여 각각의 스트림을 정의한다. 익명 Observable을 만들때 유용하고, 간단한 Observable을 만들려면 이렇게 진행하는 방식이 괜찮은 것 같다. 자세한 내용은 [여기](https://qiita.com/toRisouP/items/86fea641982e6e16dac6#observablecreate)를 참조하세요.

## 사용 예

실제로 지금 개발하고있는 제품에서 코드를 발췌 해 보았습니다.

유저 등록시 플레이어 이름 (ScreenName)도 함께 등록하는 코드입니다.
```cs
/// <summary>
/// 유저의 신규 등록을 한다
/// </summary>
public void SignUp(string id, string mail, string pass, string screenName)
{
    // 플레이어의 표시 이름을 등록 할 때 설정
    // 회원 테이블에 미리 필드를 늘려 둘 필요가 있다
    var optionalData = new Dictionary<string, object>()
    {
        {"screenName",screenName }
    };

    // 사용자의 신규 등록
    ObservableNcmbUserAuth
        .SingUpAsync(mail, id, pass, optionalData)
        .Subscribe(user =>
        {
            // NCMBUser 인스턴스를 저장
            UserProfileProvider.SetUser(user);
            // 로그인 성공 통지
            onSuccessLoginSubject.OnNext(Unit.Default);
        },
        // 오류 발생시 오류 메시지를 통지
        ex => failedReasonSubject.OnNext(ConvertExceptionToText((NCMBException)ex)));
}
```

실행하면 이렇게 기본 정보에 따라 screenName 필드도 설정됩니다

![](/images/unity3d/2019-10-29-1.jpeg)

비동기 처리와 콜백 함수를 실행하는 것은 IObservable로 변환 하면, 모두 UniRx로 취급 할 수 있도록 하면 매우 용이 하기 때문에 추천합니다.