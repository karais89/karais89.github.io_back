---
layout: post
title: "[Codewars #63]  Find the smallest integer in the array (8kyu)"
excerpt: "[Codewars #63]  Find the smallest integer in the array (8kyu) 문제 풀이"
date: 2019-01-31 11:31:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/55a2d7ebe362935a210000b2/train/csharp)

Given an array of integers your solution should find the smallest integer.

For example:

- Given [34, 15, 88, 2] your solution will return 2
- Given [34, -345, -1, 100] your solution will return -345

You can assume, for the purpose of this kata, that the supplied array will not be empty.

## My Solution

```csharp
public class Kata
{
    public static int FindSmallestInt(int[] args)
    {
      int min = int.MaxValue;
      for (int i = 0; i < args.Length; i++)
      {
        if (min > args[i])
        {
          min = args[i];
        }
      }
      return min;
    }
}
```

- 가장 작은 값을 구하는 간단한 문제다.

## Best Practices 1

```csharp
using System.Linq;

public class Kata
{
    public static int FindSmallestInt(int[] args)
    {
      return args.Min();
    }
}
```

- Linq의 Min 메서드를 사용

## Best Practices 2

```csharp
using System.Linq;

public class Kata
{
    public static int FindSmallestInt(int[] args) => args.Min();

}
```

- 위와 똑같은 방식인데 C# 에서 새로 추가된 기능으로 더 짧게 사용할 수 있다.