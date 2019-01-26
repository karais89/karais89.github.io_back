---
layout: post
title: "[Codewars #55] Convert number to reversed array of digits (8kyu)"
excerpt: "[Codewars #55] Convert number to reversed array of digits (8kyu) 문제 풀이"
date: 2019-01-26 16:20:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/5583090cbe83f4fd8c000051/train/csharp)

Convert number to reversed array of digits
Given a random number:

C#: long;
C++: unsigned long;
You have to return the digits of this number within an array in reverse order.

Example:
```
348597 => [7,9,5,8,4,3]
```

## My Solution

```csharp
using System;
using System.Collections.Generic;

namespace Solution
{
  class Digitizer
  {
    public static long[] Digitize(long n)
    {
      // Code goes here
      string str = n.ToString();
      char[] chArr= str.ToCharArray();
      Array.Reverse(chArr);
      long[] longArr = new long[chArr.Length];
      for (int i = 0; i < longArr.Length; i++)
      {
        long number;
        if (Int64.TryParse(chArr[i].ToString(), out number))
        {
          longArr[i] = number;
        }
      }
      return longArr;
    }
  }
}
```

- 8 kyu의 수준을 잘 모르겠다.
- long형 변수를 스트링으로 변환한다. 그 후 char 배열로 변환하고 역순의 배열을 만든후에 각 변수들을 long 형으로 변환후 반환한다.

## Best Practices

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

namespace Solution
{
  class Digitizer
  {
    public static long[] Digitize(long n)
    {
      return n.ToString()
              .Reverse()
              .Select(t => Convert.ToInt64(t.ToString()))
              .ToArray();
    }
  }
}
```

- linq 사용.
- 역순으로 만든 후. 각 값을 순회 하면서 long형으로 변환 해준후 배열로 반환해준다.