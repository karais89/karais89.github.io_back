---
layout: post
title: "[Codewars #20] Steps in Primes (6kyu)"
excerpt: "[Codewars #20] Steps in Primes (6kyu) 문제 풀이"
date: 2019-01-08 22:23:00 +0900
tags: [codewars, kata]
---

## Instructions

The prime numbers are not regularly spaced. For example from 2 to 3 the step is 1. From 3 to 5 the step is 2. From 7 to 11 it is 4. Between 2 and 50 we have the following pairs of 2-steps primes:

```
3, 5 - 5, 7, - 11, 13, - 17, 19, - 29, 31, - 41, 43
```

We will write a function step with parameters:

- g (integer >= 2) which indicates the step we are looking for,
- m (integer >= 2) which gives the start of the search (m inclusive),
- n (integer >= m) which gives the end of the search (n inclusive)

In the example above step(2, 2, 50) will return [3, 5] which is the first pair between 2 and 50 with a 2-steps.

So this function should return the first pair of the two prime numbers spaced with a step of g between the limits m, n if these g-steps prime numbers exist otherwise nil or null or None or Nothing or [] or "0, 0" or {0, 0} (depending on the language).

Examples:

```
step(2, 5, 7) --> [5, 7] or (5, 7) or {5, 7} or "5 7"

step(2, 5, 5) --> nil or ... or [] in Ocaml or {0, 0} in C++

step(4, 130, 200) --> [163, 167] or (163, 167) or {163, 167}
```

See more examples for your language in "RUN"
Remarks:
([193, 197] is also such a 2-steps primes between 130 and 200 but it's not the first pair).

step(6, 100, 110) --> [101, 107] though there is a prime between 101 and 107 which is 103; the pair 101-103 is a 2-step.

Notes: The idea of "step" is close to that of "gap" but it is not exactly the same. For those interested they can have a look at http://mathworld.wolfram.com/PrimeGaps.html.

A "gap" is more restrictive: there must be no primes in between (101-107 is a "step" but not a "gap". Next kata will be about "gaps":-).

For Go: nil slice is expected when there are no step between m and n. Example: step(2,4900,4919) --> nil

## My Solution

```csharp
using System;
using System.Collections.Generic;

class StepInPrimes
{
    public static List<long> GetPrimeList(long start, long end)
    {
      long[] arr = new long[end+1];
      for (long i = 0; i < arr.Length; i++)
      {
        arr[i] = i;
      }

      for (long i = 2; i < arr.Length; i++)
      {
        // 이미 체크된 수의 배수는 확인하지 않는다
        if (arr[i] == 0)
        {
          continue;
        }

        // i를 제외한 i의 배수들은 0으로 체크
        for (long j = i + i; j < arr.Length; j += i)
        {
          arr[j] = 0;
        }
      }

      List<long> newList = new List<long>(arr);
      newList.RemoveAll(item => item == 0);
      newList.RemoveAll(item => item == 1);
      newList.RemoveAll(item => item < start);

      return newList;
    }

    public static long[] Step(int g, long m, long n)
    {
        List<long> primeNumbers = GetPrimeList(m, n);
        for (int i = 0; i < primeNumbers.Count; i++)
        {
          for (int j = i+1; j < primeNumbers.Count; j++)
          {
            if (primeNumbers[j] - primeNumbers[i] == g)
            {
              return new long[] { primeNumbers[i], primeNumbers[j] };
            }
          }
        }

        return null;
    }
}
```


처음에 그냥 소수를 그냥 구하는 식으로 해서 문제를 풀려고 했는데
타임아웃이 걸려서 결국 다른 방법으로 문제를 풀어야 했다.
사실 소수를 찾는 방법 중 가장 유명한 [에라토스테네스의 체](https://ko.wikipedia.org/wiki/%EC%97%90%EB%9D%BC%ED%86%A0%EC%8A%A4%ED%85%8C%EB%84%A4%EC%8A%A4%EC%9D%98_%EC%B2%B4)를 이미 알고 있었기 때문에 해당 방법을 찾아서 문제를 풀었다.

## Best Practices

```csharp
using System;
class StepInPrimes
{
    public static long[] Step(int g, long m, long n)
        {
            for (long i = m; i <= n; i++)
            {
                if (isPrime(i))
                {
                    if (isPrime(i + g) && (i + g) <= n)
                    {
                        return new long[2] { i, i + g };
                    }
                }
            }
            return null;
        }

        public static bool isPrime(long number)
        {
            if (number == 1) return false;
            if (number == 2) return true;

            if (number % 2 == 0) return false; //Even number

            for (int i = 3; i <= Math.Ceiling(Math.Sqrt(number)); i += 2)
            {
                if (number % i == 0) return false;
            }
            return true;
        }
}
```

제일 높은 표를 받은 해결책이긴 한데 표 자체가 4개 밖에 없어서 추가 검증이 필요하다.
에라토스테네스의 체의 공식을 C#에서 적용하는 다른 소스등을 참고하는게 도움이 될 듯하다. 결국 문제의 핵심은 소수를 구하는 것이다.

아래는 스택 오버플로우에서 찾은 소수 인지 판단하는 함수

https://stackoverflow.com/questions/15743192/check-if-number-is-prime-number

```
public static bool IsPrime(int number)
{
    if (number <= 1) return false;
    if (number == 2) return true;
    if (number % 2 == 0) return false;

    var boundary = (int)Math.Floor(Math.Sqrt(number));

    for (int i = 3; i <= boundary; i+=2)
        if (number % i == 0)
            return false;

    return true;
}
```