---
layout: post
title: "GitHub 를 통한 UE4 소스 다운로드와 빌드"
excerpt: "GitHub 를 통한 UE4 소스 다운로드와 빌드"
date: 2018-10-31 21:00:00 +0900
tags: [unreal]
---

[GitHub 를 통한 UE4 소스 다운로드와 빌드](https://www.youtube.com/watch?v=usjlNHPn-jo)

4.20.3 버전 기준

다른 부분 위주로 설명

## 사전 준비
1. [Unreal Engine 사이트 가입](https://www.unrealengine.com)
2. [GitHub 사이트 가입](http://github.com/)
3. [GitHub client 다운로드](http://windows.github.com/)

## 순서

### GitHub 연결 방법

[Unreal Engine 연결된 계정](https://www.unrealengine.com/account/connected)에서 GitHub 연결

![unreal connected image](/images/unreal/181031-01.jpg)

<br>

GitHub에 연결된 이메일 확인 (에픽 게임즈 조직 가입 이메일)

![github join image](/images/unreal/181031-02.jpg)

### GitHub 연결 후 실행 방법

- 가입 후 https://github.com/EpicGames/UnrealEngine 해당 레포지토리에 접근 가능 하다
- 해당 레포지토리를 fork 한다.
- fork한 레포지토리를 로컬에 clone 한다.
- Setup.bat 실행
- GenerateProjectFiles.bat 실행
- UE4.sln 솔루션 파일 visual studio 2017 오픈
- 빌드 (15분 정도 걸린다)
- UnrealEngine\Engine\Binaries\Win64 폴더에 UE4Editor.exe 실행