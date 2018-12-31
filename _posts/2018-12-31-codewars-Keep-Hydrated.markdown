---
layout: post
title: "[Codewars #4] Keep Hydrated! (8kyu)"
excerpt: "[Codewars #4] Keep Hydrated! (8kyu) 문제 풀이"
date: 2018-12-31 22:53:00 +0900
tags: [codewars, kata]
---

## Instructions

Nathan loves cycling.

Because Nathan knows it is important to stay hydrated, he drinks 0.5 litres of water per hour of cycling.

You get given the time in hours and you need to return the number of litres Nathan will drink, rounded to the smallest value.

For example:

time = 3 ----> litres = 1

time = 6.7---> litres = 3

time = 11.8--> litres = 5

## My Solution

```csharp
using System;

public class Kata
{
  public static int Litres(double time)
  {
    int intTime = (int)time;

    //The fun begins here.
    return (intTime / 2);
  }
}
```

어렵지 않게 해결.

## Best Practices

```csharp
using System;

public class Kata
{
  public static int Litres(double time)
  {
    return (int)(time/2);
  }
}
```

내 해결 방법과 유사하다.

## Best Practices 2

```csharp
using System;

public class Kata
{
  public static int Litres(double time) => (int)(time*0.5);

}
```

2번째로 좋은 Best Practices인데..

C# 6.0 에서 새로 추가된 Expression-bodied member 기능을 사용하여 한줄로 표현 할 수 있다.