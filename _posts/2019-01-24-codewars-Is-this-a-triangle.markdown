---
layout: post
title: "[Codewars #50] Is this a triangle? (7kyu)"
excerpt: "[Codewars #50] Is this a triangle? (7kyu) 문제 풀이"
date: 2019-01-24 23:37:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/56606694ec01347ce800001b/train/csharp)

Implement a method that accepts 3 integer values a, b, c. The method should return true if a triangle can be built with the sides of given length and false in any other case.

(In this case, all triangles must have surface greater than 0 to be accepted).

## My Solution

```csharp
using System;

public class Triangle
{
    public static bool IsTriangle(int a, int b, int c)
    {
      int[] triangles = new int[3];

      triangles[0] = a;
      triangles[1] = b;
      triangles[2] = c;

      Array.Sort(triangles);

      return triangles[0] + triangles[1] > triangles[2];
    }
}
```

- 삼각형의 성립 조건을 알아야 풀 수 있는 문제.
- c가 가장 긴 변일때 a+b > c
- [Array.Sort](https://docs.microsoft.com/ko-kr/dotnet/api/system.array.sort?view=netframework-4.7.2)

## Best Practices

```csharp
public class Triangle
{
    public static bool IsTriangle(int a, int b, int c) =>
      a > 0 && b > 0 && c > 0 && a + b > c && a + c > b && b + c > a;
}
```

- 삼각형의 성립조건은 저게 맞는 것 같은데.
- 어차피 모든 조건을 성립하면 삼각형의 성립 조건도 성립되기 때문에 상관없이 구현되어 있는 것 같다.