---
layout: post
title: "[Codewars #56] Remove First and Last Character (8kyu)"
excerpt: "[Codewars #56] Remove First and Last Character (8kyu) 문제 풀이"
date: 2019-01-26 16:21:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/56bc28ad5bdaeb48760009b0/train/csharp)

It's pretty straightforward. Your goal is to create a function that removes the first and last characters of a string. You're given one parameter, the original string. You don't have to worry with strings with less than two characters.

## My Solution

```csharp
using System;

        public class Kata
        {
            public static string Remove_char(string s)
            {
                // Your Code
                return s.Substring(1, s.Length - 2);
            }
        }
```

- [c# Substring](https://docs.microsoft.com/ko-kr/dotnet/api/system.string.substring) 메서드를 사용하면 쉽게 해결 가능

## Best Practices

```csharp
using System;

        public class Kata
        {
            public static string Remove_char(string s)
            {
                return s.Substring(1,(s.Length - 2));
            }
        }
```

- 똑같은 방식으로 해결