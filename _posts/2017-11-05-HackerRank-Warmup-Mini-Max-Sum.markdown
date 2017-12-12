---
layout: post
title: "[HackerRank #8] Warmup - Mini-Max Sum"
excerpt: "HackerRank Warmup Mini-Max Sum 문제 풀이"
date: 2017-11-05 12:54:00 +0900
tags: [hackerrank]
---

## 문제 요약

5개의 정수가 주어지고 그 중에 4개를 선택할때 나올 수 있는 최소값과 최대값을 구해라.

Sample Input
```
1 2 3 4 5
```

Sample Output
```
10 14
```


## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static void Main(String[] args) {
        string[] arr_temp = Console.ReadLine().Split(' ');
        int[] arr = Array.ConvertAll(arr_temp,Int32.Parse);
        
        int min = arr[0];
        int max = arr[0];
        long sum = 0;
        for (int i = 0; i < arr.Length; i++)
        {
            if (min > arr[i])
            {
                min = arr[i];
            }
            
            if (max < arr[i])
            {
                max = arr[i];
            }
            
            sum += arr[i];
        }
        
        Console.Write("{0} {1}", sum-max, sum-min);
    }
}
```

## 느낀점

문제 자체는 5개 중에 4개를 선택해서 나올 수 있는 최소값과 최대값을 구하는 것이다.

하지만 이걸 다른식으로 생각하면 5개 중에 가장 큰 수를 선택해서 뺀 값이 최소값 5개 중에 가장 작은 값을 구해서 뺀 값이 최대값이다.