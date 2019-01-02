---
layout: post
title: "[Codewars #7] Remove the minimum (7kyu)"
excerpt: "[Codewars #7] Remove the minimum (7kyu) 문제 풀이"
date: 2019-01-03 01:37:00 +0900
tags: [codewars, kata]
---

## Instructions

The museum of incredible dull things

The museum of incredible dull things wants to get rid of some exhibitions. Miriam, the interior architect, comes up with a plan to remove the most boring exhibitions. She gives them a rating, and then removes the one with the lowest rating.

However, just as she finished rating all exhibitions, she's off to an important fair, so she asks you to write a program that tells her the ratings of the items after one removed the lowest one. Fair enough.


Task

Given an array of integers, remove the smallest value. Do not mutate the original array/list. If there are multiple elements with the same value, remove the one with a lower index. If you get an empty array/list, return an empty array/list.

Don't change the order of the elements that are left.

```
Remover.RemoveSmallest(new List<int>{1,2,3,4,5}) = new List<int>{2,3,4,5}
Remover.RemoveSmallest(new List<int>{5,3,2,1,4}) = new List<int>{5,3,2,4}
Remover.RemoveSmallest(new List<int>{2,2,1,2,1}) = new List<int>{2,2,2,1}
```

## My Solution

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Remover
{
  public static List<int> RemoveSmallest(List<int> numbers)
  {
    // Good Luck!
    if (numbers == null || numbers.Count == 0)
    {
      return new List<int>() { };
    }

    int minVal = int.MaxValue;
    int minIndex = 0;
    for (int i = 0; i < numbers.Count; i++)
    {
      if (minVal > numbers[i])
      {
        minVal = numbers[i];
        minIndex = i;
      }
    }

    List<int> removeSmallests = new List<int>(numbers);
    removeSmallests.RemoveAt(minIndex);
    return removeSmallests;
  }
}
```

문제 자체는 전달 받은 리스트의 값 변경 없이 최소 값을 뺀 리스트를 반환하는 문제이다.

전달 받은 리스트에서 최소값을 구하고, 그 리스트를 복사한후 최소값을 구한 인덱스를 빼준후 반환해 주는 식으로 구했다.

## Best Practices

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Remover
{
  public static List<int> RemoveSmallest(List<int> numbers)
  {
    numbers.Remove(numbers.DefaultIfEmpty().Min());
    return numbers;
  }
}
```

Linq를 사용하여 해결 하였다.
하지만 목록을 복제하지는 않아서, 문제에서 요구하는 완벽한 해결 방법은 아닌거 같다.


DefaultIfEmpty
- IEnumerable<T>의 요소를 반환하거나, 시퀀스가 비어 있으면 기본값이 할당된 singleton 컬렉션을 반환합니다


DefaultIfEmpty 메서드는 객체 자체가 null 일때의 예외 처리도 함께 되어 있다.
```
public static IEnumerable<TSource> DefaultIfEmpty<TSource>(this IEnumerable<TSource> source, TSource defaultValue)
{
  if (source == null)
    throw Error.ArgumentNull(nameof (source));
  return Enumerable.DefaultIfEmptyIterator<TSource>(source, defaultValue);
}
```