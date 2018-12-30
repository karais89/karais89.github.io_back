---
layout: post
title: "[Codewars #2] Count of positives / sum of negatives (8kyu)"
excerpt: "[Codewars #2] Count of positives / sum of negatives (8kyu) 문제 풀이"
date: 2018-12-30 22:35:00 +0900
tags: [codewars, kata]
---

## Instructions

Given an array of integers.

Return an array, where the first element is the count of positives numbers and the second element is sum of negative numbers.

If the input array is empty or null, return an empty array.

## My Solution

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Kata
{
    public static int[] CountPositivesSumNegatives(int[] input)
    {
        if (input == null || input.Length == 0)
        {
            return new int[] { };
        }

        int positiveCount = 0;
        int negativeSum = 0;
        for (int i = 0; i < input.Length; i++)
        {
            if (input[i] > 0)
            {
                positiveCount++;
            }
            else
            {
                negativeSum += input[i];
            }
        }

        return new int[] { positiveCount, negativeSum };
    }
}
```

배열에서 양수의 경우 양수가 몇개 있는지 구하고, 음수의 경우 음수값 합을 구하여 배열로 반환하라.


## Best Practices

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Kata
{
    public static int[] CountPositivesSumNegatives(int[] input)
    {
        if(input == null || !input.Any())
        {
            return new int[] {};
        }

        int countPositives = input.Count(n => n > 0);
        int sumNegatives = input.Where(n => n < 0).Sum();

        return new int[] { countPositives, sumNegatives };
    }
}
```

Linq를 사용하여 해결.
Codwars에서 C# 문제의 경우 Best Practices의 경우 Linq를 사용하여 풀이한 정답이 채택되는 것 같다.
논리 자체는 Linq를 사용하지 않아도 충분히 해결 가능하지만 Linq 자체의 장점이 분명히 존재하기 때문에 Linq쪽으로 해결하면 Best Practices로 채택되는 것 같다.