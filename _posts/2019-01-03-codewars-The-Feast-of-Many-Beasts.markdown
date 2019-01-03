---
layout: post
title: "[Codewars #9] The Feast of Many Beasts (8kyu)"
excerpt: "[Codewars #9] The Feast of Many Beasts (8kyu) 문제 풀이"
date: 2019-01-03 16:48:00 +0900
tags: [codewars, kata]
---

## Instructions

All of the animals are having a feast! Each animal is bringing one dish. There is just one rule: the dish must start and end with the same letters as the animal's name. For example, the great blue heron is bringing garlic naan and the chickadee is bringing chocolate cake.

Write a function feast that takes the animal's name and dish as arguments and returns true or false to indicate whether the beast is allowed to bring the dish to the feast.

Assume that beast and dish are always lowercase strings, and that each has at least two letters. beast and dish may contain hyphens and spaces, but these will not appear at the beginning or end of the string. They will not contain numerals.

## My Solution

```csharp
public class Kata
{
    public static bool Feast(string beast, string dish)
    {  
        return beast[0] == dish[0] && beast[beast.Length-1] == dish[dish.Length-1];
    }
}```


맨 처음 문자열과 맨 마지막 문자열만 같으면 된다.

## Best Practices 1

```csharp
public class Kata
{
    public static bool Feast(string beast, string dish)
    {
        return beast[0] == dish[0] && beast[beast.Length - 1] == dish[dish.Length - 1];
    }
}
```

푸는 방식이 완전히 동일하여 다른 방식이 있나 보니 Linq를 사용하는 방식이 있다.

Linq를 사용하는 방식이 더 직관적인 것 같은데.. 이런걸 보면 무슨 기준으로 표를 주는건지 잘 모르겠다..

## Best Practices 2

```csharp
using System.Linq;

public class Kata
{
    public static bool Feast(string beast, string dish) => beast.First() == dish.First() && beast.Last() == dish.Last();
}
```