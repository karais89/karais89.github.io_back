---
layout: post
title: "[Codewars #34] Reverse or rotate? (6kyu)"
excerpt: "[Codewars #34] Reverse or rotate? (6kyu) 문제 풀이"
date: 2019-01-18 00:45:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/56b5afb4ed1f6d5fb0000991/train/csharp)

The input is a string str of digits. Cut the string into chunks (a chunk here is a substring of the initial string) of size sz (ignore the last chunk if its size is less than sz).

If a chunk represents an integer such as the sum of the cubes of its digits is divisible by 2, reverse that chunk; otherwise rotate it to the left by one position. Put together these modified chunks and return the result as a string.

If

- sz is <= 0 or if str is empty return ""
- sz is greater (>) than the length of str it is impossible to take a chunk of size sz hence return "".

Examples:

```
revrot("123456987654", 6) --> "234561876549"
revrot("123456987653", 6) --> "234561356789"
revrot("66443875", 4) --> "44668753"
revrot("66443875", 8) --> "64438756"
revrot("664438769", 8) --> "67834466"
revrot("123456779", 8) --> "23456771"
revrot("", 8) --> ""
revrot("123456779", 0) --> ""
revrot("563000655734469485", 4) --> "0365065073456944"
```

## My Solution

```csharp
using System;
using System.Text;
using System.Linq;

public class Revrot
{
    public static string InternalRevRot(string str)
    {
      if (String.IsNullOrEmpty(str))
      {
        return string.Empty;
      }

      int sum = 0;
      for (int i = 0; i < str.Length; i++)
      {
        sum += int.Parse(str[i].ToString());
      }

      StringBuilder strBuilder = new StringBuilder();
      if (sum % 2 == 0)
      {
        for (int i = str.Length - 1; i >= 0; i--)
        {
          strBuilder.Append(str[i]);
        }
        return strBuilder.ToString();
      }

      for (int i = 1; i < str.Length; i++)
      {
        strBuilder.Append(str[i]);
      }
      strBuilder.Append(str[0]);
      return strBuilder.ToString();
    }

    public static string RevRot(string strng, int sz)
    {
        if (sz <= 0 || String.IsNullOrEmpty(strng) || strng.Length < sz)
        {
          return string.Empty;
        }

        StringBuilder strBuilder = new StringBuilder();
        for (int i = 0; i < strng.Length; i += sz)
        {
          if (i + sz < strng.Length)
          {
            string str = strng.Substring(i, sz);
            strBuilder.Append(InternalRevRot(str));
          }
        }
        return strBuilder.ToString();
    }
}
```


일단 영어 해석이 안되서 헤맸다. 천천히 문제를 다시 읽어 봤다.

주어진 sz 매개변수 만큼 substring으로 문자열을 가르고
각각의 문자열의 합을 기준으로 다음과 같은 기준으로 판단
1. 문자열의 총 합이 짝수이면 숫자 뒤집기. (1->2->3 순서일때 3->2->1)
2. 문자열의 합이 홀수이면 왼쪽으로 한칸 이동시키기. (1->2->3 순서일때 2->3->1)


## Best Practices

```csharp
using System;
using System.Linq;

public class Revrot
{
    public static string RevRot(string strng, int sz)
    {
        if (String.IsNullOrEmpty(strng) || sz <= 0 || sz > strng.Length)
            return String.Empty;

        return
            new String(
                Enumerable.Range(0, strng.Length/sz)
                    .Select(i => strng.Substring(i*sz, sz))
                    .Select(
                        chunk =>
                            chunk.Sum(digit => (int) Math.Pow(int.Parse(digit.ToString()), 3))%2 == 0
                                ? chunk.Reverse()
                                : chunk.Skip(1).Concat(chunk.Take(1)))
                    .SelectMany(x => x)
                    .ToArray());
    }
}
```


chunk.Reverse() 이 부분에 해당
```
      StringBuilder strBuilder = new StringBuilder();
      if (sum % 2 == 0)
      {
        for (int i = str.Length - 1; i >= 0; i--)
        {
          strBuilder.Append(str[i]);
        }
        return strBuilder.ToString();
      }
```

chunk.Skip(1).Concat(chunk.Take(1)) 이 부분에 해당
```
      for (int i = 1; i < str.Length; i++)
      {
        strBuilder.Append(str[i]);
      }
      strBuilder.Append(str[0]);
      return strBuilder.ToString();
```