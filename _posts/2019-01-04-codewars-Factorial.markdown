---
layout: post
title: "[Codewars #12] Factorial (7kyu)"
excerpt: "[Codewars #12] Factorial (7kyu) 문제 풀이"
date: 2019-01-04 19:44:00 +0900
tags: [codewars, kata]
---

## Instructions

In mathematics, the factorial of a non-negative integer n, denoted by n!, is the product of all positive integers less than or equal to n. For example: 5! = 5 * 4 * 3 * 2 * 1 = 120. By convention the value of 0! is 1.

Write a function to calculate factorial for a given input. If input is below 0 or above 12 throw an exception of type

```
ArgumentOutOfRangeException (C#) or IllegalArgumentException (Java) or RangeException (PHP) or throw a RangeError (JavaScript).
```

More details about factorial can be found here: http://en.wikipedia.org/wiki/Factorial

## My Solution

```csharp
using System;

public static class Kata
{
  public static int Factorial(int n)
  {
    if (n < 0 || n > 12)
      throw new ArgumentOutOfRangeException("invalid value");

    int sum = 1;
    for (int i = 1; i <= n; i++)
    {
      sum *= i;
    }
    return sum;
  }
}
```

팩토리얼 문제.
예외를 발생시키는 코드는 사실 잘 사용하지 않아서.. 예외 발생 부분은 찾아봤다.

## Best Practices 1

```csharp
using System;

public static class Kata
{
  public static int Factorial(int n)
  {
    if(n < 0 || n > 12)
      throw new ArgumentOutOfRangeException();
    return n > 0 ? n * Factorial(n - 1) : 1;
  }
}
```

재귀 함수 사용. 재귀 함수 부분도 생각해 보았는데..
속된말로 ~~스택 뽕빨 난다..~~란 말이 있어서 그냥 시도 조차 안했다.
사실 문제에서는 12개 까지의 값을 제한 하기 때문에 어떻게 본다면 이게 가장 좋은 코드 일 수도 있을 것 같다.

## Best Practices 2

```csharp
using System;
using System.Linq;

public static class Kata
{
  public static int Factorial(int n)
  {
    if(n < 0 || n > 12) throw new ArgumentOutOfRangeException();

    return Enumerable.Range(1, n).Aggregate(1, (x,y) => x * y);
  }
}
```

Aggregate란 메서드는 처음 봄.

확인해보니 누산기에 값을 누적해준다고 생각하면 될 듯.

1부터 시작해서 n까지의 값을 누적해서 계속 곱해준다고 생각하면 될듯.

https://docs.microsoft.com/ko-kr/dotnet/api/system.linq.enumerable.aggregate?view=netframework-4.7.2