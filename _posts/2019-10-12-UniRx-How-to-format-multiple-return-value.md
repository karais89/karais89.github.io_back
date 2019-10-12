---
layout: post
title: "스메소드의 반환 값에 여러 형식을 지정하는 방법 [C#]"
excerpt: "메소드의 반환 값에 여러 형식을 지정하는 방법 [C#] 번역"
date: 2019-10-12 09:13:00 +0900
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

원문 : [https://qiita.com/toRisouP/items/49ed5a052e8be4386c9e](https://qiita.com/toRisouP/items/49ed5a052e8be4386c9e)

이 포스팅은 원문을 단순히 구글 번역을 하여 정리한 내용입니다. 일본어를 잘하시는 분은 원문을 보시는게 더 좋으실 것 같습니다. 

---

Unity 보다는 C#의 이야기 입니다. C# 메서드의 반환 값에 하나의 유형만 지정할 수 없습니다.

[역주] [C# 7.0 부터는 Tuple라는 스펙이 추가되어 반환 값에 여러가지 유형을 지정할 수 있습니다.](https://docs.microsoft.com/ko-kr/dotnet/csharp/tuples)

그래서 여러 매개 변수를 메서드로 반환하는 경우 어떻게 하면 좋은가를 소개하고 싶습니다.

## 스스로 새로운 클래스나 구조체를 정의한다.

**가장 무난하고 전통적인 방법.** 스스로 그러한 여러 유형을 내부에 있는 클래스나 구조체로 만들어 버린다. 용도에 맞게 일일이 클래스를 정의하는 것은 상당히 귀찮은 것이 단점이지만, 코드의 가독성은 상당히 높아진다.

[Option](https://www.scala-lang.org/api/2.9.1/scala/Option.html)형이나 [Case Class](https://docs.scala-lang.org/ko/tutorials/tour/case-classes.html.html) 같은 것이 C#에도 있으면 더 편할 것 같습니다.

```cs
struct LoadDataModel
{
    public bool IsSuccess { get; set; }
    public string Data { get; set; }

    public LoadDataModel(bool isSuccess, string data)
    {
        IsSuccess = isSuccess;
        Data = data;
    }
}

private void Start()
{
    var loadData = LoadData();
    if (loadData.IsSuccess)
    {
        Debug.Log(loadData.Data);
    }
}

/// <summary>
/// 데이터를 로드 한다.
/// </summary>
/// <returns> </returns>
private LoadDataModel LoadData()
{
    try
    {
        // 뭔가를 로드 하는 처리
        var data = "로드 된 데이터";
        return new LoadDataModel(true, data);
    }
    catch (Exception e)
    {
        return new LoadDataModel(false, null);
    }
}
```

## out 키워드를 사용

[Physics.Raycast](https://docs.unity3d.com/kr/current/ScriptReference/Physics.Raycast.html) 등에서도 등장 out을 사용하는 형태.

메서드의 인수 정의에 out을 지정하여 그 인수를 출력하는 변수를 선언 할 수 있습니다.

단지 out을 사용하면 코드의 가독성이 떨어져서, 저는 별로 좋아하지 않습니다.

```cs
private void Start()
{
    string loadData;
    var isSuccessed = LoadData(out loadData);
    if (isSuccessed)
    {
        Debug.Log(loadData);
    }
}

/// <summary>
/// 데이터를 로드 한다.
/// </summary>
/// <param name="data">로드 성공시 값</param>
/// <returns>로드 성공 여부</returns>
private bool LoadData(out string data)
{
    try
    {
        // 뭔가 로드 처리
        data = "로드 된 데이터";
        // 성공하면 true 반환
        return true;
    }
    catch (Exception e)
    {
        data = null;
        // 예외가 나오면 false 반환
        return false;
    }
}
```

[역주] C# 7.0 부터 [inline out](https://docs.microsoft.com/ko-kr/dotnet/csharp/whats-new/csharp-7#out-variables) 기능이 추가되어 조금 더 가독성 있는 코드를 작성 할 수 있게 되었습니다.

```cs
private void Start()
{
    if (LoadData(out var loadData))
    {
        Debug.Log(loadData);
    }
}
```

## Tuple을 사용한다. -

~~Tuple (튜플)을 사용하는 방법. 그러나 C# 표준 Tuple은 Unity에서 사용할 수 없습니다. UniRx는 UniRx.Tuple로 호환되는 것을 만들었습니다. Tuple을 사용한다면 UniRx를 도입하고 UniRx.Tuple을 사용합시다.~~

```cs
protected void Start()
{
    var result = LoadData();
    if (result.Item1)
    {
        var loadData = result.Item2;
        Debug.Log(loadData);
    }

}

private Tuple<bool, string> LoadData()
{
    try
    {
        // 여기에서 로드 처리
        return new Tuple<bool, string>(true,("로드된 데이터"));
    }
    catch
    {
        return new Tuple<bool, string>(false, null);
    }
}
```

[net 4.0 이상 버전에서는 아예 UniRx.Tuple는 사용하지 못하고,](https://github.com/neuecc/UniRx/blob/7.1.0/Assets/Plugins/UniRx/Scripts/System/Tuple.cs#L7) C#에서 기본적으로 제공하는 Tuple을 사용해야 합니다.

## Tuple을 사용한다 - C# 7.0 이상

```cs
private void Start()
{
    var result = LoadData();
    if (result.Item1)
    {
        var loadData = result.Item2;
        Debug.Log(loadData);
    }

        // 위와 같은 형태, 명시적으로 변수명 선언
        var (isSuccesses, loadData2) = LoadData();
    if (isSuccesses)
    {
        Debug.Log(loadData2);
    }
}

private (bool isSuccessed, string data) LoadData()
{
    try 
    { 
        // 여기서 로드 처리
        return (true, "로드 된 데이터");
    } 
    catch 
    { 
        return (false, null);
    }
}
```

C# 7.0 부터 제공되는 Tuple 기능을 사용하는 방식입니다. Tuple을 사용해야 되는 경우라면 C#에서 제공해주는 Tuple을 사용하면 됩니다.

## 정리

**클래스나 구조체를 정의해서 사용하는 것이 가장 간단한 설계라고 생각 합니다.**

out이나 Tuple은 남용하면 코드의 가독성과 유지 보수성이 떨어지므로, 사용할때는 주의해서 사용하도록 합시다.