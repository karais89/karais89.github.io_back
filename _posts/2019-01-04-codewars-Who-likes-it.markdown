---
layout: post
title: "[Codewars #11] Who likes it? (6kyu)"
excerpt: "[Codewars #11] Who likes it? (6kyu) 문제 풀이"
date: 2019-01-04 19:42:00 +0900
tags: [codewars, kata]
---

## Instructions

You probably know the "like" system from Facebook and other pages. People can "like" blog posts, pictures or other items. We want to create the text that should be displayed next to such an item.

Implement a function likes :: [String] -> String, which must take in input array, containing the names of people who like an item. It must return the display text as shown in the examples:

```
Kata.Likes(new string[0]) => "no one likes this"
Kata.Likes(new string[] {"Peter"}) => "Peter likes this"
Kata.Likes(new string[] {"Jacob", "Alex"}) => "Jacob and Alex like this"
Kata.Likes(new string[] {"Max", "John", "Mark"}) => "Max, John and Mark like this"
Kata.Likes(new string[] {"Alex", "Jacob", "Mark", "Max"}) => "Alex, Jacob and 2 others like this"
```

For 4 or more names, the number in and 2 others simply increases.

## My Solution

```csharp
using System;

public static class Kata
{
  public static string Likes(string[] name)
  {
    if (name == null || name.Length == 0)
    {
      return "no one likes this";
    }
    else if (name.Length == 1)
    {
      return $"{name[0]} likes this";
    }
    else if (name.Length == 2)
    {
      return $"{name[0]} and {name[1]} like this";
    }
    else if (name.Length == 3)
    {
      return $"{name[0]}, {name[1]} and {name[2]} like this";
    }
    return $"{name[0]}, {name[1]} and {name.Length-2} others like this";
  }
}
```

풀면서 이런 해결 방법 밖에 없나? 라는 생각을 했는데.. 다른 방법은 없나 보다.

결국엔 if else 다.. 원래는 string.Format을 사용하려다가..

그냥 $ 표현을 사용 하였다.

$ 기호는 C# 6.0에서 추가된 [문자열 보간 (Interpolated Strings)](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/tokens/interpolated) 이다.

## Best Practices

```csharp
public static class Kata
{
  public static string Likes(string[] names)
  {
    switch (names.Length)
    {
      case 0: return "no one likes this"; // :(
      case 1: return $"{names[0]} likes this";
      case 2: return $"{names[0]} and {names[1]} like this";
      case 3: return $"{names[0]}, {names[1]} and {names[2]} like this";
      default: return $"{names[0]}, {names[1]} and {names.Length - 2} others like this";
    }
  }
}
```

if와 switch의 차이 정도..
나의 코드에서는 names가 null이 아니라는 보장이 없어서, null 검사를 해줬었는데 여기서는 그런 null 체크 코드는 없다.