---
layout: post
title: "[Codewars #61] Maximum subarray sum (5kyu)"
excerpt: "[Codewars #61] Maximum subarray sum (5kyu) 문제 풀이"
date: 2019-01-30 12:57:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/54521e9ec8e60bc4de000d6c/train/csharp)

The maximum sum subarray problem consists in finding the maximum sum of a contiguous subsequence in an array or list of integers:

```
maxSequence [-2, 1, -3, 4, -1, 2, 1, -5, 4]
-- should be 6: [4, -1, 2, 1]
```

Easy case is when the list is made up of only positive numbers and the maximum sum is the sum of the whole array. If the list is made up of only negative numbers, return 0 instead.

Empty list is considered to have zero greatest sum. Note that the empty list or array is also a valid sublist/subarray.

## My Solution

```csharp
public static class Kata {

  public static bool IsAllNativeInteger(int[] arr)
  {
    for (int i = 0; i < arr.Length; i++)
    {
      if (arr[i] >= 0)
      {
        return false;
      }
    }
    return true;
  }

  public static int MaxSequence(int[] arr) {
    if (arr == null || arr.Length == 0 || IsAllNativeInteger(arr))
    {
      return 0;
    }

    int maxSum = 0;
    for (int i = 0; i < arr.Length; i++)
    {
      int max = 0;
      for (int j = i; j < arr.Length; j++)
      {
        max += arr[j];
        if (max > maxSum)
        {
          maxSum = max;
        }
      }
    }

    return maxSum;
  }
}
```

- 5kyu 문제 자체는 상당히 짧은데 생각해야 되는 것들이 좀 있다.
- 일단 가장 먼저 생각난건 처음부터 연속된 숫자를 조회하면서 가장 큰 수가 나올때 까지 계속 수를 더해 가는 방법.
- 이 방법 이외의 방법은 딱히 생각 나지 않는다.

## Best Practices

```csharp
public static class Kata
{
    public static int MaxSequence(int[] arr)
    {
        int max = 0, res = 0, sum = 0;
        foreach(var item in arr)
        {
            sum += item;
            max = sum > max ? max : sum;
            res = res > sum - max ? res : sum - max;
        }
        return res;
    }
}
```

- for문을 한번만 쓰고 해결이 가능한 문제인가보다.