---
layout: post
title: "[Codewars #15] Are they the "same"? (6kyu)"
excerpt: "[Codewars #15] Are they the "same"? (6kyu) 문제 풀이"
date: 2019-01-06 18:26:00 +0900
tags: [codewars, kata]
---

## Instructions

Given two arrays a and b write a function comp(a, b) (compSame(a, b) in Clojure) that checks whether the two arrays have the "same" elements, with the same multiplicities. "Same" means, here, that the elements in b are the elements in a squared, regardless of the order.

Examples
Valid arrays

```
a = [121, 144, 19, 161, 19, 144, 19, 11]
b = [121, 14641, 20736, 361, 25921, 361, 20736, 361]
```

comp(a, b) returns true because in b 121 is the square of 11, 14641 is the square of 121, 20736 the square of 144, 361 the square of 19, 25921 the square of 161, and so on. It gets obvious if we write b's elements in terms of squares:

```
a = [121, 144, 19, 161, 19, 144, 19, 11]
b = [11*11, 121*121, 144*144, 19*19, 161*161, 19*19, 144*144, 19*19]
```

Invalid arrays
If we change the first number to something else, comp may not return true anymore:

```
a = [121, 144, 19, 161, 19, 144, 19, 11]
b = [132, 14641, 20736, 361, 25921, 361, 20736, 361]
```

comp(a,b) returns false because in b 132 is not the square of any number of a.

```
a = [121, 144, 19, 161, 19, 144, 19, 11]
b = [121, 14641, 20736, 36100, 25921, 361, 20736, 361]
```

comp(a,b) returns false because in b 36100 is not the square of any number of a.

Remarks
a or b might be [] (all languages except R, Shell). a or b might be nil or null or None or nothing (except in Haskell, Elixir, C++, Rust, R, Shell).

If a or b are nil (or null or None), the problem doesn't make sense so return false.

If a or b are empty the result is evident by itself.

## My Solution

```csharp
using System;
using System.Collections.Generic;

class AreTheySame
{
  public static bool comp(int[] a, int[] b)
  {
    if (a == null || b == null)
    {
      return false;
    }

    if (a.Length != b.Length)
    {
      return false;
    }

    List<int> bList = new List<int>(b);
    for (int i = 0; i < a.Length; i++)
    {
      int mulVal = a[i] * a[i];
      for (int j = 0; j < bList.Count; j++)
      {
        if (bList[j] == mulVal)
        {
          bList.RemoveAt(j);
          break;
        }
      }
    }

    return bList.Count == 0;
  }
}
```


b 배열을 리스트로 만든 후 값이 일치할 경우 bList에서 값을 제거해주는 방식.
맨 마지막에 bList에 크기가 0이면 a와 모두 일치하다고 판단하고 true를 리턴해주고 아니면 모두 일치하지는 않으므로 false를 리턴해준다.

```
if (a.Length != b.Length)
{
return false;
}
```

이 부분은 필요 없어도 동작 할듯.

## Best Practices

```csharp
using System;
using System.Linq;

class AreTheySame
{
  public static bool comp(int[] a, int[] b)
  {
    if ((a == null) || (b == null)){
      return false;
    }

    int[] copy = a.Select(x => x * x).ToArray();
    Array.Sort(copy);
    Array.Sort(b);

    return copy.SequenceEqual(b);
  }
}
```

Linq를 사용하였다.
각 값을 제곱한 배열을 만든 후 배열의 값을 모두 정렬 시킨 후 (Select)
일치하는지 확인하는 함수([SequenceEqual](https://docs.microsoft.com/ko-kr/dotnet/api/system.linq.enumerable.sequenceequal?view=netframework-4.7.2))를 사용하여 해결 하였다.


> Select() 메서드는 데이타를 변형하거나 부분 선택하여 새로운 클래스(Anonymous Type)를 만들어 리턴하고 싶은 때 사용한다.