---
layout: post
title: "[Codewars #14] Sum of a sequence (7kyu)"
excerpt: "[Codewars #14] Sum of a sequence (7kyu) 문제 풀이"
date: 2019-01-05 14:01:00 +0900
tags: [codewars, kata]
---

## Instructions

Your task is to make function, which returns the sum of a sequence of integers.

The sequence is defined by 3 non-negative values: begin, end, step.

If begin value is greater than the end, function should returns 0

Examples
```
SequenceSum (2,2,2); // => 2
SequenceSum (2,6,2); // => 12 -> 2 + 4 + 6
SequenceSum (1,5,1); // => 15 -> 1 + 2 + 3 + 4 + 5
SequenceSum (1,5,3); // => 5 -> 1 + 4
This is the first kata in the series:
```
1) Sum of a sequence (this kata)
2) Sum of a Sequence [Hard-Core Version]

## My Solution

```csharp
public static class Kata 
{
  public static int SequenceSum(int start, int end, int step)
  {
    int sum = 0;
    for (int i = start; i <= end; i += step)
    {
      sum += i;
    }
    return sum;
  }
}
```

기본적인 for문의 사용 방법만 알면 풀 수 있는 문제.

## Best Practices 1

```csharp
public static class Kata
{
  public static int SequenceSum(int start, int end, int step) 
  {
    int sum = 0;

    for ( int i = start; i <= end; i += step)
    {
        sum += i;
    }

    return sum;
  }
}
```

답은 일치 한다.

## Best Practices 2

```csharp
public static class Kata
{
  public static int SequenceSum(int start, int end, int step)
  {
    return start == end ? end : start > end ? 0 : SequenceSum(start + step, end, step) + start;
  }
}
```

표 자체는 많이 받지 못한 해답인데. 재귀 함수를 사용하여 푸는 방식을 사용 하였다.