---
layout: post
title: "[Codewars #18] Maximum Length Difference (7kyu)"
excerpt: "[Codewars #18] Maximum Length Difference (7kyu) 문제 풀이"
date: 2019-01-07 14:18:00 +0900
tags: [codewars, kata]
---

## Instructions

You are given two arrays a1 and a2 of strings. Each string is composed with letters from a to z. Let x be any string in the first array and y be any string in the second array.

Find max(abs(length(x) − length(y)))

If a1 and/or a2 are empty return -1 in each language except in Haskell (F#) where you will return Nothing (None).

Example:

```
a1 = ["hoqq", "bbllkw", "oox", "ejjuyyy", "plmiis", "xxxzgpsssa", "xxwwkktt", "znnnnfqknaz", "qqquuhii", "dvvvwz"]
a2 = ["cccooommaaqqoxii", "gggqaffhhh", "tttoowwwmmww"]
mxdiflg(a1, a2) --> 13
```

Bash note:
- input : 2 strings with substrings separated by ,
- output: number as a string

## My Solution

```csharp
using System;

public class MaxDiffLength
{

    public static int Mxdiflg(string[] a1, string[] a2)
    {
        if (a1 == null || a1.Length == 0 || a2 == null || a2.Length == 0)
          return -1;

        int maxVal = int.MinValue;
        for (int i = 0; i < a1.Length; i++)
        {
          for (int j = 0; j < a2.Length; j++)
          {
            int diff = Math.Abs(a1[i].Length - a2[j].Length);
            if (diff > maxVal)
              maxVal = diff;
          }
        }

        return maxVal;
    }
}
```

두 배열의 길이의 값 차이 중 가장 큰 값을 구하는 문제

## Best Practices

```csharp
using System;
using System.Linq;

public class MaxDiffLength
{

    public static int Mxdiflg(string[] a1, string[] a2)
    {
        if(a1.Length <= 0 || a2.Length <= 0)
          return -1;
        var first = Math.Abs(a1.Max(x => x.Length) - a2.Min(x => x.Length));
        var second = Math.Abs(a2.Max(x => x.Length) - a1.Min(x => x.Length));
        return Math.Max(first, second);
    }
}
```

Linq 사용

a1 최대값 - a2 최소값 혹은 a2 최대값 - a1 최소값 중에 가장 큰 값 반환