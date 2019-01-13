---
layout: post
title: "[Codewars #30]  Playing with digits (6kyu)"
excerpt: "[Codewars #30]  Playing with digits (6kyu) 문제 풀이"
date: 2019-01-13 14:19:00 +0900
tags: [codewars, kata]
---

## Instructions

Some numbers have funny properties. For example:

```
89 --> 8¹ + 9² = 89 * 1

695 --> 6² + 9³ + 5⁴= 1390 = 695 * 2

46288 --> 4³ + 6⁴+ 2⁵ + 8⁶ + 8⁷ = 2360688 = 46288 * 51
```

Given a positive integer n written as abcd... (a, b, c, d... being digits) and a positive integer p we want to find a positive integer k, if it exists, such as the sum of the digits of n taken to the successive powers of p is equal to k * n. In other words:

```
Is there an integer k such as : (a ^ p + b ^ (p+1) + c ^(p+2) + d ^ (p+3) + ...) = n * k
```

If it is the case we will return k, if not return -1.

Note: n, p will always be given as strictly positive integers.

```
digPow(89, 1) should return 1 since 8¹ + 9² = 89 = 89 * 1
digPow(92, 1) should return -1 since there is no k such as 9¹ + 2² equals 92 * k
digPow(695, 2) should return 2 since 6² + 9³ + 5⁴= 1390 = 695 * 2
digPow(46288, 3) should return 51 since 4³ + 6⁴+ 2⁵ + 8⁶ + 8⁷ = 2360688 = 46288 * 51
```

## My Solution

```csharp
using System;

public class DigPow {
 public static long digPow(int n, int p) {
  string strN = n.ToString();
    long sum = 0;
    for (int i = 0; i < strN.Length; i++)
    {
      int digit = int.Parse(strN[i].ToString());
      long powVal = (long)Math.Pow(digit, p + i);
      sum += powVal;
    }

    if (sum % n == 0)
    {
      return sum / n;
    }

    return -1;
 }
}
```

1. int형을 String으로 변환
2. 각 자릿수 대로 제곱 해준후 더해준다.
3. 더해준 값이 n으로 나누어지면 몫 반환. 아니면 -1 반환

## Best Practices

```csharp
using System.Linq;
using System;

public class DigPow {
  public static long digPow(int n, int p) {
    var sum = Convert.ToInt64(n.ToString().Select(s => Math.Pow(int.Parse(s.ToString()), p++)).Sum());
    return sum % n == 0 ? sum / n : -1;
  }
}
```

Linq는 2줄이면 해결