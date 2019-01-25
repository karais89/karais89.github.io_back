---
layout: post
title: "[Codewars #53] List Filtering (7kyu)"
excerpt: "[Codewars #53] List Filtering (7kyu) 문제 풀이"
date: 2019-01-26 01:56:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/53dbd5315a3c69eed20002dd/train/csharp)

In this kata you will create a function that takes a list of non-negative integers and strings and returns a new list with the strings filtered out.

Example
```
ListFilterer.GetIntegersFromList(new List<object>(){1, 2, "a", "b"}) => {1, 2}
ListFilterer.GetIntegersFromList(new List<object>(){1, 2, "a", "b", 0, 15}) => {1, 2, 0, 15}
ListFilterer.GetIntegersFromList(new List<object>(){1, 2, "a", "b", "aasf", "1", "123", 231}) => {1, 2, 231}
```

## My Solution

```csharp
using System.Collections;
using System.Collections.Generic;

public class ListFilterer
{
   public static IEnumerable<int> GetIntegersFromList(List<object> listOfItems)
   {
     List<int> newInts = new List<int>();
     foreach (var item in listOfItems)
     {
       if (item is int)
       {
         newInts.Add((int)item);
       }
     }
     return newInts;
   }
}
```

- c# 자료형 판단? 리플렉션을 사용해야 되나?
- is 연산자로 가능?
- newInts 말고 filterInts 정도로 네이밍 변경을 했으면 좋았겠다.

## Best Practices

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;

public class ListFilterer
{
   public static IEnumerable<int> GetIntegersFromList(List<object> listOfItems)
   {
      return listOfItems.OfType<int>();
   }
}
```

- [Linq의 OfType 자체에서 필터링 기능을 제공](https://docs.microsoft.com/ko-kr/dotnet/api/system.linq.enumerable.oftype?view=netframework-4.7.2)