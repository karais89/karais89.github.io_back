---
layout: post
title: "[Codewars #3] Sum without highest and lowest number (8kyu)"
excerpt: "[Codewars #3] Sum without highest and lowest number (8kyu) 문제 풀이"
date: 2018-12-31 22:51:00 +0900
tags: [codewars, kata]
---

## Instructions

Sum all the numbers of the array (in F# and Haskell you get a list) except the highest and the lowest element (the value, not the index!).

(The highest/lowest element is respectively only one element at each edge, even if there are more than one with the same value!)

```
{ 6, 2, 1, 8, 10 } => 16
{ 1, 1, 11, 2, 3 } => 6
```

If array is empty, null or None, or if only 1 Element exists, return 0.
Note:In C++ instead null an empty vector is used. In C there is no null. ;-) 

```
-- There's no null in Haskell, therefore Maybe [Int] is used. Nothing represents null.
```

## My Solution

```csharp
using System;
using System.Linq;

public static class Kata
{
  public static int Sum(int[] numbers)
  {
    if (numbers == null || numbers.Length == 0)
    {
      return 0;
    }

    int minVal = numbers[0];
    int maxVal = numbers[0];
    for (int i = 1; i < numbers.Length; i++)
    {
        if (numbers[i] < minVal)
        {
            minVal = numbers[i];
        }

        if (numbers[i] > maxVal)
        {
            maxVal = numbers[i];
        }
    }

    int sum = 0;
    bool isCheckMinVal = false;
    bool isCheckMaxVal = false;
    for (int i = 0; i < numbers.Length; i++)
    {
        if (numbers[i] == minVal && !isCheckMinVal)
        {
            isCheckMinVal = true;
            continue;
        }

        if (numbers[i] == maxVal && !isCheckMaxVal)
        {
            isCheckMaxVal = true;
            continue;
        }
        sum += numbers[i];
    }
    return sum;
  }
}
```

8kyu 문제인데, 너무 어렵게 풀었다..
생각해보면 문제 자체가 배열의 합에서 가장 큰 수와 가장 작은 수를 빼라는 문제인데.
가장 큰 수와 가장 작은 수의 배열 자체를 구하려고 해서 문제 자체를 좀 어렵게 풀이 했다.
잘못된 방법인듯.

## Best Practices

```csharp
using System;
using System.Linq;

public static class Kata
{
    public static int Sum(int[] numbers)
    {
        return numbers == null || numbers.Length < 2
            ? 0
            : numbers.Sum() - numbers.Max() - numbers.Min();
    }
}
```

Linq를 사용하면 한줄이면 해결된다..