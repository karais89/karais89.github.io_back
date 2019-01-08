---
layout: post
title: "[Codewars #19] Build Tower (6kyu)"
excerpt: "[Codewars #19] Build Tower (6kyu) 문제 풀이"
date: 2019-01-08 22:20:00 +0900
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

FUNDAMENTALSNUMBERS

Poweredby qualified
Solution:

class StepInPrimes
{
    public static long[] Step(int g, long m, long n)
    {
        // your code
    }
}
Sample Tests:

using System;
using NUnit.Framework;

[TestFixture]
public static class StepInPrimesTests {

[Test]
    public static void test1() {

## My Solution

```csharp
using System;
using System.Text;
public class Kata
{
  public static string[] TowerBuilder(int nFloors)
  {
    string[] towers = new string[nFloors];
    int totalCnt = nFloors * 2 - 1;
    for (int i = 0; i < nFloors; i++)
    {
      int spaceCnt = (nFloors - 1 - i) * 2;
      int starCnt = totalCnt - spaceCnt;
      towers[i] = MakeTowerStr(spaceCnt, starCnt);
    }
    return towers;
  }
  private static string MakeTowerStr(int spaceCnt, int starCnt)
  {
      int halfSpaceCnt = spaceCnt / 2;
      StringBuilder sb = new StringBuilder();
      // before space
      for (int i = 0; i < halfSpaceCnt; i++)
      {
        sb.Append(" ");
      }
      // star
      for (int i = 0; i < starCnt; i++)
      {
        sb.Append("*");
      }
      // after space
      for (int i = 0; i < halfSpaceCnt; i++)
      {
        sb.Append(" ");
      }
      return sb.ToString();
  }
}
```

학부때 많이 했던 별 찍기 문제.

## Best Practices

```csharp
public class Kata
{
  public static string[] TowerBuilder(int nFloors)
  {
    var result = new string[nFloors];
    for(int i=0;i<nFloors;i++)
      result[i] = string.Concat(new string(' ',nFloors - i - 1),
                                new string('*',i * 2 + 1),
                                new string(' ',nFloors - i - 1));
    return result;
  }
}
```

Concat 및 new String의 2번째 인자를 사용하여 깔끔하게 풀었다.
String 2번째 인자가 반복할 개수를 받을 수 있는걸 몰라서 괜히 어렵게 풀었다.
문제를 풀때 msdn 한번씩 뒤져보는것도 좋을 수 있겠다.

https://docs.microsoft.com/ko-kr/dotnet/api/system.string?view=netframework-4.7.2


```
// Create a string that consists of a character repeated 20 times.
string string2 = new string('c', 20);
```