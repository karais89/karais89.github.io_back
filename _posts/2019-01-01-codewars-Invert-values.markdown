---
layout: post
title: "[Codewars #5] Invert values (8kyu)"
excerpt: "[Codewars #5] Invert values (8kyu) 문제 풀이"
date: 2019-01-01 23:22:00 +0900
tags: [codewars, kata]
---

## Instructions

Given a set of numbers, return the additive inverse of each. Each positive becomes negatives, and the negatives become positives.

```
invert([1,2,3,4,5]) == [-1,-2,-3,-4,-5]
invert([1,-2,3,-4,5]) == [-1,2,-3,4,-5]
invert([]) == []
```

## My Solution

```csharp
using System.Linq;
namespace Solution
{
  public static class ArraysInversion
  {
    public static int[] InvertValues(int[] input)
    {
      //Code it!
      int[] inversArray = new int[input.Length];
      for (int i = 0; i < input.Length; i++)
      {
        inversArray[i] = -input[i];
      }
      return inversArray;
    }
  }
}
```

새로운 배열을 만들고, 역이 되는 정수를 저장하고 반환.

## Best Practices

```csharp
using System.Linq;
namespace Solution
{
  public static class ArraysInversion
  {
    public static int[] InvertValues(int[] input)
    {
      return input.Select(n => -n).ToArray();
    }
  }
}
```

Linq의 Select 함수를 사용하여 역을 만들고 그 역을 배열로 반환해준다.