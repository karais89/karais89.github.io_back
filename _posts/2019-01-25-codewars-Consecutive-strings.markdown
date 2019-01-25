---
layout: post
title: "[Codewars #51] Consecutive strings (6kyu)"
excerpt: "[Codewars #51] Consecutive strings (6kyu) 문제 풀이"
date: 2019-01-25 23:52:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/56a5d994ac971f1ac500003e/train/csharp)

You are given an array strarr of strings and an integer k. Your task is to return the first longest string consisting of k consecutive strings taken in the array.

Example:
longest_consec(["zone", "abigail", "theta", "form", "libe", "zas", "theta", "abigail"], 2) --> "abigailtheta"

n being the length of the string array, if n = 0 or k > n or k <= 0 return "".

Note
consecutive strings : follow one after another without an interruption

## My Solution

```csharp
using System;
using System.Collections.Generic;

public class LongestConsecutives
{
    public static String LongestConsec(string[] strarr, int k)
    {
        // your code
        if (strarr == null || strarr.Length == 0 ||
            strarr.Length < k || k <= 0)
        {
          return string.Empty;
        }

        int maxLength = 0;
        int longestIndex = 0;
        for (int i = 0; i < strarr.Length; i++)
        {
          int length = 0;
          for (int j = i; j < strarr.Length && j < i + k; j++)
          {
            length += strarr[j].Length;
          }

          if (length > maxLength)
          {
            maxLength = length;
            longestIndex = i;
          }

        }
        string str = "";
        for (int i = longestIndex; i < longestIndex + k; i++)
        {
          str += strarr[i];
        }
        return str;
    }
}
```

- 영어 해석이 안되서 무슨 조건인지 정확히 모르겠다.
- k개의 연속된 문자열 길이의 합이 가장 긴 문자열을 구하면 된다.
- stringBuilder를 사용하는 부분은 일단 생략.

## Best Practices 1

```csharp
using System;
using System.Linq;
public class LongestConsecutives {
    public static string LongestConsec(string[] s, int k){
        return s.Length==0||s.Length<k||k<=0 ? ""
             : Enumerable.Range(0,s.Length-k+1)
                         .Select(x=>string.Join("",s.Skip(x).Take(k)))
                         .OrderByDescending(x=>x.Length)
                         .First();
    }
}
```

- linq 사용

## Best Practices 2

```csharp
 public class LongestConsecutives
        {
            public static string LongestConsec(string[] strarr, int k)
            {
                if (k > strarr.Length || strarr.Length == 0 || k <= 0)
                {
                    return string.Empty;
                }

                var currentStr = string.Empty;
                for (var i = 0; i < strarr.Length; i++)
                {
                    var str = string.Empty;
                    for (var j = i; j < k + i && j < strarr.Length; j++)
                    {
                        str += strarr[j];
                    }

                    if (currentStr.Length < str.Length)
                    {
                        currentStr = str;
                    }
                }

                return currentStr;
            }
        }
```

- 논리는 거의 비슷한듯.
