---
layout: post
title: "[HackerRank #3] Warmup - Compare the Triplets"
description: "HackerRank Warmup Compare the Triplets 문제 풀이"
date: 2017-10-31 16:12:00 +0900
tags: [hackerrank]
---

## 문제 요약

두개의 배열에서 

A = (a0, a1, a2)

B = (b0, b1, b2)

a0 > b0 이면 a가 1점을 얻음

a0 == b0 이면 아무도 점수를 얻지 못함

a0 < b0 이면 b가 1점을 얻음

a와 b가 얻은 총 점수를 순서대로 출력 하는 문제

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int[] solve(int a0, int a1, int a2, int b0, int b1, int b2){
        // Complete this function
        int sumA = 0;
        int sumB = 0;
        
        int[] arrA = new int[] { a0, a1, a2 };
        int[] arrB = new int[] { b0, b1, b2 };
        
        for (int i = 0; i < arrA.Length; i++)
        {
            if (arrA[i] > arrB[i])
                sumA += 1;
            else if (arrA[i] < arrB[i])
                sumB += 1;
        }
        return new int[] { sumA, sumB };
    }

    static void Main(String[] args) {
        string[] tokens_a0 = Console.ReadLine().Split(' ');
        int a0 = Convert.ToInt32(tokens_a0[0]);
        int a1 = Convert.ToInt32(tokens_a0[1]);
        int a2 = Convert.ToInt32(tokens_a0[2]);
        string[] tokens_b0 = Console.ReadLine().Split(' ');
        int b0 = Convert.ToInt32(tokens_b0[0]);
        int b1 = Convert.ToInt32(tokens_b0[1]);
        int b2 = Convert.ToInt32(tokens_b0[2]);
        int[] result = solve(a0, a1, a2, b0, b1, b2);
        Console.WriteLine(String.Join(" ", result));
    }
}
```

## 느낀점

배열의 값을 비교하고 그 값을 변수에 각각 저장해 주면 된다.