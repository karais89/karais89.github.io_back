---
layout: post
title: "[Codewars #32] Persistent Bugger (6kyu)"
excerpt: "[Codewars #32] Persistent Bugger (6kyu) 문제 풀이"
date: 2019-01-15 17:58:00 +0900
tags: [codewars, kata]
---

## Instructions

Write a function, persistence, that takes in a positive parameter num and returns its multiplicative persistence, which is the number of times you must multiply the digits in num until you reach a single digit.

For example:

```
 persistence(39) == 3 // because 3*9 = 27, 2*7 = 14, 1*4=4
                      // and 4 has only one digit

 persistence(999) == 4 // because 9*9*9 = 729, 7*2*9 = 126,
                       // 1*2*6 = 12, and finally 1*2 = 2

 persistence(4) == 0 // because 4 is already a one-digit number
```

## My Solution

```csharp
using System;
using System.Collections.Generic;

public class Persist
{
  public static long SumList(List<int> list)
  {
    long sum = 1;
    foreach (int num in list)
    {
      sum *= num;
    }
    return sum;
  }

  public static bool CheckPersistenceList(List<int> list)
  {
    if (list.Count <= 1)
    {
      return false;
    }

    return true;
  }

  public static int RecusviePersistence(long n, int count)
  {
    List<int> toInts = new List<int>();
    string strNum = n.ToString();
    for (int i = 0; i < strNum.Length; i++)
    {
      toInts.Add(int.Parse(strNum[i].ToString()));
    }

    long sum = SumList(toInts);
    Console.WriteLine("n: " + n + ", sum: " + sum);
    if (CheckPersistenceList(toInts))
    {
      count++;
      return RecusviePersistence(sum, count);
    }
    else
    {
      return count;
    }
  }

 public static int Persistence(long n)
 {
    return RecusviePersistence(n, 0);
 }
}
```

뭔가 쓸데 없이 길어지는 느낌..


## Best Practices

```csharp
using System;
using System.Linq;

public class Persist
{
  public static int Persistence(long n)
  {
      int count = 0;
      while (n > 9)
      {
          count++;
          n = n.ToString().Select(digit => int.Parse(digit.ToString())).Aggregate((x, y) => x * y);
      }
      return count;
  }
}
```

Linq 사용