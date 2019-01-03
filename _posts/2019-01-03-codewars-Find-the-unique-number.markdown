---
layout: post
title: "[Codewars #10] Find the unique number (6kyu)"
excerpt: "[Codewars #10] Find the unique number (6kyu) 문제 풀이"
date: 2019-01-03 16:49:00 +0900
tags: [codewars, kata]
---

## Instructions

There is an array with some numbers. All numbers are equal except for one. Try to find it!

```
findUniq([ 1, 1, 1, 2, 1, 1 ]) === 2
findUniq([ 0, 0, 0.55, 0, 0 ]) === 0.55
```

It’s guaranteed that array contains more than 3 numbers.

The tests contain some very huge arrays, so think about performance.

This is the first kata in series:

1. Find the unique number (this kata)
2. Find the unique string
3. Find The Unique

## My Solution

```csharp
using System.Collections.Generic;
using System.Linq;

public class Kata
{
  public static int GetUnique(IEnumerable<int> numbers)
  {
    int count = -1;
    int firstNum = 0;
    int diffCount = 0;
    int findNum = 0;
    foreach (int num in numbers)
    {
      if (++count == 0)
      {
        firstNum = num;
        continue;
      }

      if (num == firstNum)
        continue;


      diffCount++;
      findNum = num;
    }

    if (diffCount == 1)
      return findNum;
    else
      return firstNum;
  }
}
```

- 배열의 개수는 3개 이상이 보증된다.
- 원소 중 1개를 제외하고는 모든 수가 똑같다. 다른 수를 리턴해야 된다.
- IEnumerable로 받아서 foreach문으로 루프를 돌렸다.
- 첫번째 원소의 숫자를 기준으로 비교를 함.

풀이 방법
1. 첫번째 원소와 다른 숫자가 1개 이상 존재시(diffCount가 1이상)에는 첫번째 원소가 유일한 수
2. 첫번째 원소와 다른 숫자가 1개 이면 findNum으로 대입된 숫자가 유일한 수 이다.

좋은 방법 같지는 않다.

## Best Practices 1

```csharp
using System.Collections.Generic;
using System.Linq;

public class Kata
{
  public static int GetUnique(IEnumerable<int> numbers)
  {
    return numbers.GroupBy(x=>x).Single(x=> x.Count() == 1).Key;
  }
}
```

Linq를 사용하면 한 줄이면 풀수 있는 문제..

## Best Practices 2

```csharp
using System.Collections.Generic;
using System.Linq;

public class Kata
{
  public static int GetUnique(IEnumerable<int> numbers)
  {
      int[] numArray = numbers.ToArray();

      int prevNum = numbers.First();
      for (int i = 0; i < numArray.Length - 1; i++)
      {
        if (numArray[i] != prevNum){
          if(numArray[i+1] == numArray[i])
            return prevNum;
          else
            return numArray[i];
        }
      }

      return 0;
  }
}
```

ToArray 함수의 경우 메모리 많은 양의 메모리를 사용한다고 한다.
문제에서는 아주 큰 배열도 가능하게 처리해달라고 하여 해당 답안은 적절치 않다고 한다.