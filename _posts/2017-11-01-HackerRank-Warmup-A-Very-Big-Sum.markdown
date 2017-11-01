---
layout: post
title: "[HackerRank #4] Warmup - A Very Big Sum"
description: "HackerRank Warmup A Very Big Sum 문제 풀이"
date: 2017-11-01 22:32:00 +0900
tags: [hackerrank]
---

## 문제 요약

첫 번째 인수는 배열의 인자 개수. 두번째 인자는 배열을 인자로 받는다.

배열의 크기의 합을 구하는 문제이다. 배열의 합은 매우 크므로 c/c++은 long long 자료형을 사용하고, 

자바의 경우 long 자료형을 사용해서 배열의 합을 구하자.

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static long aVeryBigSum(int n, long[] ar) {
        // Complete this function
        long sum = 0;
        for (int i = 0; i < n ; i++)
        {
            sum += ar[i];
        }
        return sum;
    }

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        string[] ar_temp = Console.ReadLine().Split(' ');
        long[] ar = Array.ConvertAll(ar_temp,Int64.Parse);
        long result = aVeryBigSum(n, ar);
        Console.WriteLine(result);
    }
}
```

## 느낀점

배열의 합을 구하면 된다.

배열의 합은 매우 크므로 long 자료형을 사용해서 구해주면 끝..

[HackerRank Warup 2번째 문제]({{ site.url }}/2017/10/30/HackerRank-Warmup-Simple-Array-Sum/)와 같은 방식으로 구했다.