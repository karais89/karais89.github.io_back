---
layout: post
title: "[Codewars #65]  Return Negative (8kyu)"
excerpt: "[Codewars #65]  Return Negative (8kyu) 문제 풀이"
date: 2019-02-09 10:43:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/55685cd7ad70877c23000102/train/csharp)

In this simple assignment you are given a number and have to make it negative. But maybe the number is already negative?

Example:
```
Kata.MakeNegative(1); // return -1
Kata.MakeNegative(-5); // return -5
Kata.MakeNegative(0); // return 0
```

Notes:

The number can be negative already, in which case no change is required.
Zero (0) is not checked for any specific sign. Negative zeros make no mathematical sense.

## My Solution

```csharp
using System;

public static class Kata
{
  public static int MakeNegative(int number)
  {
    return number > 0 ? -number : number;
  }
}

```

- 오랜만에 코드워 문제를 다시 풀었다.
- 난이도 자체는 말도 안되게 쉬운 문제이고, 다양한 방법으로 풀 수 있을 것이라고 기대를 하고 만든 문제인듯 하다.
- 일단 난 내가 생각하는 정석적인 방법으로 문제를 풀겠다.
- 이정도 문제의 경우 [삼항 연산자](https://ko.wikipedia.org/wiki/%3F:)를 사용하는게 깔끔할 것 같다.

## Best Practices

```csharp
using System;

public static class Kata
{
  public static int MakeNegative(int number)
  {
    return -Math.Abs(number);
  }
}
```

- 문제 자체가 무조건 마이너스(-)를 붙이는 문제라, 이게 더 직관적인 해결 방법인 것 같다.
- 내가 푼 방식은 2번째로 표를 많이 받았다.