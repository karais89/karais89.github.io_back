---
layout: post
title: "[HackerRank #2] Warmup - Simple Array Sum"
excerpt: "HackerRank Warmup Simple Array Sum 문제 풀이"
date: 2017-10-30 21:47:00 +0900
tags: [hackerrank]
---

## 문제 요약

첫 번째 인수에는 배열의 크기가, 두번째 인수에는 배열이 들어온다. 배열의 합을 구해라.

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int simpleArraySum(int n, int[] ar) {
        // Complete this function
        int sum = 0;
        for (int i = 0; i < n; i++)
        {
            sum += ar[i];
        }
        return sum;
    }

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        string[] ar_temp = Console.ReadLine().Split(' ');
        int[] ar = Array.ConvertAll(ar_temp,Int32.Parse);
        int result = simpleArraySum(n, ar);
        Console.WriteLine(result);
    }
}
```

## 느낀점

워밍업 단계.

가장 기초적인 배열의 합 구하는 문제.

배열의 크기를 첫번째 인자로 넘겨주는 이유는 C언어 같은 언어는 배열의 크기를 알 수 없기 때문인 것 같다.