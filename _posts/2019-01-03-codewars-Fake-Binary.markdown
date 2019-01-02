---
layout: post
title: "[Codewars #8] Fake Binary (8kyu)"
excerpt: "[Codewars #8] Fake Binary (8kyu) 문제 풀이"
date: 2019-01-03 01:38:00 +0900
tags: [codewars, kata]
---

## Instructions

Given a string of digits, you should replace any digit below 5 with '0' and any digit 5 and above with '1'. Return the resulting string.

## My Solution

```csharp
public class Kata
{
  public static string FakeBin(string x)
  {
    string copyX = "";
    for (int i = 0; i < x.Length; i++)
    {
      if (x[i] < '5')
          copyX += '0';
      else
          copyX += '1';
    }
    return copyX;
  }
}
```

처음에 문제 자체를 이해를 못해서, 테스트 코드를 보고 이해 한 후 해결 하였다.

해결하고 보니 string을 for문에서 계속 더해주는 문제가 있네..


StringBuilder을 쓸걸 그랬다..

그리고 비교 자체를 문자열 자체로 비교한게 문제가 있지 않을까 싶었는데.. 그냥 다들 그렇게 하네..

## Best Practices 1

```csharp
using System.Linq;

public class Kata
{
  public static string FakeBin(string x)
  {
    return string.Concat(x.Select(a => a < '5' ? "0" : "1"));
  }
}
```

Linq를 사용하여 해결 하였다.

## Best Practices 2

```csharp
using System.Text;
public class Kata
{
  public static string FakeBin(string x)
  {
    StringBuilder builder = new StringBuilder();

    foreach (char t in x)
    {
      builder.Append( t >= '5' ? '1' : '0' );
    }

    return builder.ToString();
  }
}
```

builder를 사용하였다.
그리고 for 대신 foreach 사용.