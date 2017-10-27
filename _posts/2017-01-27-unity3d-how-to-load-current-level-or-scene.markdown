---
layout: post
title: "Unity3d 현재 씬을 다시 불러오는 방법"
description: "유니티에서 현재 씬을 다시 불러오는 방법에 대한 설명"
date: "2017-01-27 16:01:05 +0900"
tags: [unity3d]
---

### 5.3 이전 버전에서 현재 씬을 불러오는 방법
- Application.LoadLevel(Application.loadedLevel)

### 5.3 이상 버전에서 현재 씬을 불러오는 방법
- SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);

5.3 이상 버전 에서는 using UnityEngine.SceneManagement를 선언해줘야 된다.

### 관련 API

- [SceneManager](https://docs.unity3d.com/ScriptReference/SceneManagement.SceneManager.html)
- [Scene](https://docs.unity3d.com/ScriptReference/SceneManagement.Scene.html)
- [SceneUtility](https://docs.unity3d.com/ScriptReference/SceneManagement.SceneUtility.html)
