---
layout: post
title: "[Codewars #40] Best travel (5kyu)"
excerpt: "[Codewars #40] Best travel (5kyu) 문제 풀이"
date: 2019-01-20 22:39:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/55e7280b40e1c4a06d0000aa/train/csharp)

John and Mary want to travel between a few towns A, B, C ... Mary has on a sheet of paper a list of distances between these towns. ls = [50, 55, 57, 58, 60]. John is tired of driving and he says to Mary that he doesn't want to drive more than t = 174 miles and he will visit only 3 towns.

Which distances, hence which towns, they will choose so that the sum of the distances is the biggest possible

to please Mary and John- ?
Example:

With list ls and 3 towns to visit they can make a choice between: [50,55,57],[50,55,58],[50,55,60],[50,57,58],[50,57,60],[50,58,60],[55,57,58],[55,57,60],[55,58,60],[57,58,60].

The sums of distances are then: 162, 163, 165, 165, 167, 168, 170, 172, 173, 175.

The biggest possible sum taking a limit of 174 into account is then 173 and the distances of the 3 corresponding towns is [55, 58, 60].

The function chooseBestSum (or choose_best_sum or ... depending on the language) will take as parameters t (maximum sum of distances, integer >= 0), k (number of towns to visit, k >= 1) and ls (list of distances, all distances are positive or null integers and this list has at least one element). The function returns the "best" sum ie the biggest possible sum of k distances less than or equal to the given limit t, if that sum exists, or otherwise nil, null, None, Nothing, depending on the language. With C++, C, Rust, Swift, Go, Kotlin return -1.

Examples:
```
ts = [50, 55, 56, 57, 58] choose_best_sum(163, 3, ts) -> 163

xs = [50] choose_best_sum(163, 3, xs) -> nil (or null or ... or -1 (C++, C, Rust, Swift, Go)

ys = [91, 74, 73, 85, 73, 81, 87] choose_best_sum(230, 3, ys) -> 228
```

## My Solution

```csharp

using System;
using System.Collections.Generic;
using System.Linq;

public static class SumOfK
{
    // https://stackoverflow.com/questions/33336540/how-to-use-linq-to-find-all-combinations-of-n-items-from-a-set-of-numbers
    public static IEnumerable<IEnumerable<T>> DifferentCombinations<T>(this IEnumerable<T> elements, int k)
    {
        return k == 0 ? new[] { new T[0] } :
          elements.SelectMany((e, i) =>
            elements.Skip(i + 1).DifferentCombinations(k - 1).Select(c => (new[] {e}).Concat(c)));
    }

    public static int? chooseBestSum(int t, int k, List<int> ls)
    {
      if (ls == null || ls.Count < k)
      {
        return null;
      }

      var sumlists = DifferentCombinations(ls, k);
      int bestSum = 0;
      foreach(var list in sumlists)
      {
        int sum = 0;
        foreach (var l in list)
        {
          sum += l;
        }

        if (bestSum <= sum && sum <= t)
        {
          bestSum = sum;
        }
      }

      if (bestSum == 0)
      {
        return null;
      }

      return bestSum;
    }
}
```

- 존은 오직 3개의 도시만 방문할 것이고, 174 마일 보다 더 달리지 않을 것이다.
- 거리의 합이 가장 큰 것을 선택 할 것이다.
- t = 최대 거리 합계
- k = 방문 가능한 도시의 수
- ls = 방문할 도시의 거리가 적혀 있는 리스트
- m개중 n개를 선택하는 방법?
- 조합을 구하는 문제인 듯
- 조합 구하는 알고리즘은 스택 오버플로우에서 참고 했다.

## Best Practices

```csharp
using System.Collections.Generic;
using System.Linq;

public static class SumOfK
{
  public static int? chooseBestSum(int t, int k, List<int> ls) =>
    ls.Combinations(k)
      .Select(c => (int?) c.Sum())
      .Where(sum => sum <= t)
      .DefaultIfEmpty()
      .Max();

  // Inspired by http://stackoverflow.com/questions/127704/algorithm-to-return-all-combinations-of-k-elements-from-n
  public static IEnumerable<IEnumerable<int>> Combinations(this IEnumerable<int> ls, int k) =>
    k == 0 ? new[] { new int[0] } :
      ls.SelectMany((e, i) =>
        ls.Skip(i + 1)
          .Combinations(k - 1)
          .Select(c => (new[] {e}).Concat(c)));
}
```


여기도 마찬가지로 조합 구하는 알고리즘은 스택 오버 플로우에서 참조 한듯.