---
layout: post
title: "[Codewars #52]  Ones and Zeros (7kyu)"
excerpt: "[Codewars #52]  Ones and Zeros (7kyu) 문제 풀이"
date: 2019-01-25 23:53:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/578553c3a1b8d5c40300037c/train/csharp)

Given an array of one's and zero's convert the equivalent binary value to an integer.

Eg: [0, 0, 0, 1] is treated as 0001 which is the binary representation of 1.

Examples:
```
Testing: [0, 0, 0, 1] ==> 1
Testing: [0, 0, 1, 0] ==> 2
Testing: [0, 1, 0, 1] ==> 5
Testing: [1, 0, 0, 1] ==> 9
Testing: [0, 0, 1, 0] ==> 2
Testing: [0, 1, 1, 0] ==> 6
Testing: [1, 1, 1, 1] ==> 15
Testing: [1, 0, 1, 1] ==> 11
```
However, the arrays can have varying lengths, not just limited to 4.

## My Solution

```csharp
using System;

namespace Solution
{
  class Kata
    {
      public static int binaryArrayToNumber(int[] BinaryArray)
        {
          int sum = 0;
          Array.Reverse(BinaryArray);
          for (int i = 0; i < BinaryArray.Length; i++)
          {
            if (BinaryArray[i] == 1)
            {
              sum += (int)Math.Pow(2, i);
            }
          }
          return sum;
        }
    }
}
```

- 2진수를 10진수로 변경 하는 문제

- array를 반대로 정렬해주고 2의 거듭제곱을 해주는 방식으로 해결 하였다,.

## Best Practices

```csharp
using System;
namespace Solution
{
  class Kata
    {
      public static int binaryArrayToNumber(int[] BinaryArray)
        {
          return Convert.ToInt32(string.Join("", BinaryArray), 2);
        }
    }
}
```

- 이게 가장 쉬운 해결책이긴 하네..
- 그냥 binaryArray를 string 자체로 변경한후 스트링을 2진수 배열의 값을 기준으로 int값으로 변경 해주는 코드