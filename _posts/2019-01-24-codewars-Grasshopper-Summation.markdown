---
layout: post
title: "[Codewars #48] Grasshopper - Summation (8kyu)"
excerpt: "[Codewars #48] Grasshopper - Summation (8kyu) 문제 풀이"
date: 2019-01-24 23:34:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/55d24f55d7dd296eb9000030/train/csharp)

Write a program that finds the summation of every number from 1 to num. The number will always be a positive integer greater than 0.

For example:
```
summation(2) -> 3
1 + 2

summation(8) -> 36
1 + 2 + 3 + 4 + 5 + 6 + 7 + 8
```

## My Solution

```csharp
 using System;

public static class Kata
{
    public static int summation(int num)
    {
      //Code here
      int sum = 0;
      for (int i = 0; i <= num; i++)
      {
        sum += i;
      }
      return sum;
    }
}
```

- 갑자기 1부터 n까지 더하는 문제가 나와서.. 일단 풀어봤다.
- 이렇게 간단한 문제도 생각하는 방식이 여러개 구나.

## Best Practices 1

```csharp
using System;

public static class Kata
{
    public static int summation(int num)
    {
      return num*(num+1)/2;
    }
}
```

- 더하는 방법 하면 딱 이걸 생각할 수도 있구나.
- 가우스의 유명한 일화..

## Best Practices 2

```csharp
using System;
using System.Linq;

public static class Kata
{
    public static int summation(int num)
    {
      return Enumerable.Range(1, num).Sum();
    }
}
```

- 이건 Enumerable에서 1부터 num까지의 합을 더해주는 식으로... 구현
- [Enumerable.Range - Microsoft Docs](https://docs.microsoft.com/ko-kr/dotnet/api/system.linq.enumerable.range?view=netframework-4.7.2)