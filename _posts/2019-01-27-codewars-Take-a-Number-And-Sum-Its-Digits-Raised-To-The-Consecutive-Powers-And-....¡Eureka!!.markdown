---
layout: post
title: "[Codewars #58] Take a Number And Sum Its Digits Raised To The Consecutive Powers And ....¡Eureka!! (6kyu)"
excerpt: "[Codewars #58] Take a Number And Sum Its Digits Raised To The Consecutive Powers And ....¡Eureka!! (6kyu) 문제 풀이"
date: 2019-01-27 22:56:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/5626b561280a42ecc50000d1/train/csharp)

The number 89 is the first integer with more than one digit that fulfills the property partially introduced in the title of this kata. What's the use of saying "Eureka"? Because this sum gives the same number.

In effect: 89 = 8^1 + 9^2

The next number in having this property is 135.

See this property again: 135 = 1^1 + 3^2 + 5^3

We need a function to collect these numbers, that may receive two integers a, b that defines the range [a, b] (inclusive) and outputs a list of the sorted numbers in the range that fulfills the property described above.

Let's see some cases:
```
sum_dig_pow(1, 10) == [1, 2, 3, 4, 5, 6, 7, 8, 9]

sum_dig_pow(1, 100) == [1, 2, 3, 4, 5, 6, 7, 8, 9, 89]
```

If there are no numbers of this kind in the range [a, b] the function should output an empty list.
```
sum_dig_pow(90, 100) == []
```

Enjoy it!!

## My Solution

```csharp
using System;
using System.Collections.Generic;
public class SumDigPower {

    public static bool IsSumDigNumber(long a)
    {
      string strA = a.ToString();
      long sum = 0;
      for (int i = 0; i < strA.Length; i++)
      {
        int digit = int.Parse(strA[i].ToString());
        sum += (long)Math.Pow(digit, i + 1);
      }
      return sum == a;
    }

    public static long[] SumDigPow(long a, long b)
    {
        // your code
        List<long> sumDigs = new List<long>();
        for (long i = a; i <= b; i++)
        {
          if (IsSumDigNumber(i))
          {
            sumDigs.Add(i);
          }
        }
        return sumDigs.ToArray();
    }
}
```

- 자리수 구하는 건 확실히 그냥 숫자를 string으로 변경하는게 가장 편한 방법인듯 하다.

## Best Practices

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
public class SumDigPower {

    public static long[] SumDigPow(long a, long b)
    {
        List<long> values = new List<long>();
        for (long x = a; x <= b; x++)
        {
          if (x.ToString().Select((c, i) => Math.Pow(Char.GetNumericValue(c), i + 1)).Sum() == x)
            values.Add(x);
        }
        return values.ToArray();
    }
}
```

- Char.GetNumericValue 메서드
- 논리는 비슷하지만 Linq 사용으로 코드가 짧아짐.