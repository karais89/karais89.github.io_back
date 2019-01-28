---
layout: post
title: "[Codewars #59] Double Char (8kyu)"
excerpt: "[Codewars #59] Double Char (8kyu) 문제 풀이"
date: 2019-01-28 09:27:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/56b1f01c247c01db92000076/train/csharp)

Given a string, you have to return a string in which each character (case-sensitive) is repeated once.

```
DoubleChar("String") == "SSttrriinngg"

DoubleChar("Hello World") == "HHeelllloo WWoorrlldd"

DoubleChar("1234!_ ") == "11223344!!__ "
```

## My Solution

```csharp
using System;
using System.Text;

public class Kata
{
  public static string DoubleChar(string s)
  {
    // your code here
    StringBuilder doubleStr = new StringBuilder();
    for (int i = 0; i < s.Length; i++)
    {
      doubleStr.Append(s[i]);
      doubleStr.Append(s[i]);
    }
    return doubleStr.ToString();
  }
}
```

- 각 문자 2번씩 반복 하기.

## Best Practices

```csharp
using System;
using System.Linq;

public class Kata
{
  public static string DoubleChar(string s)
  {
    return string.Join("", s.Select(x => "" + x + x));
  }
}
```

- Linq 사용.