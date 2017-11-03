---
layout: post
title: "[HackerRank #6] Warmup - Plus Minus"
description: "HackerRank Warmup Plus Minus 문제 풀이"
date: 2017-11-03 19:20:00 +0900
tags: [hackerrank]
---

## 문제 요약

첫번째 인풋은 입력받을 정수의 개수
나머지 인풋은 그 정수의 값들을 받는다.

3개의 라인으로 출력해라.

1. 양수의 개수 / 정수의 개수
2. 음수의 개수 / 정수의 개수
3. 0의 개수 / 정수의 개수

소수 6째 자리까지 표현 해야된다.


Sample Input

```
6
-4 3 -9 0 4 1      
```

- 양수 : 3개(3,4,1)
- 음수 : 2개(-4,-9)
- 0 : 1개(0)

Sample OutPut

```
0.500000
0.333333
0.166667
```

1. 3/6 = 0.500000
2. 2/6 = 0.333333
3. 1/6 = 0.166667

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        string[] arr_temp = Console.ReadLine().Split(' ');
        int[] arr = Array.ConvertAll(arr_temp,Int32.Parse);
        
        int plusCount = 0;
        int minusCount = 0;
        int zeroCount = 0;
        
        for (int i = 0; i < n ; i++)
        {
            if (arr[i] > 0)
                plusCount++;
            else if (arr[i] < 0)
                minusCount++;
            else
                zeroCount++;                
        }
        
        Console.WriteLine(string.Format("{0:f6}", (float)plusCount/n));
        Console.WriteLine(string.Format("{0:f6}", (float)minusCount/n));
        Console.WriteLine(string.Format("{0:f6}", (float)zeroCount/n));        
    }
}
```

## vatsalchanana의 답안

```cpp
#include<iostream>

using namespace std;

int main() {
    int n;
    cin >> n;

    float pl = 0, mn = 0, zr = 0;

    for (int i = 0; i < n; i++) {
        int val;
        cin >> val;
        zr += (val == 0);
        pl += (val > 0);
        mn += (val < 0);
    }
    
    zr = zr / (double)n;
    pl = pl / (double)n;
    mn = mn / (double)n;
    
    printf("%0.06lf\n%0.06lf\n%0.06lf\n", pl, mn, zr);
    return 0;
}
```

## 느낀점

문제 자체는 크게 어려운게 없다.

콘솔 출력시 서식문자를 가지고 출력하는 방법만 알면 쉬운 문제다.