---
layout: post
title: "[HackerRank #12] Implementation - Apple and Orange"
excerpt: "HackerRank Implementation Apple and Orange 문제 풀이"
date: 2017-11-08 07:44:00 +0900
tags: [hackerrank]
---

## 문제 요약

Input 설명

1. 첫 번째 라인에는 s,t (s와 t는 샘의 집의 범위)
2. 두 번째 라인에는 a,b (a는 사과위치, b는 오렌지위치)
3. 세 번째 라인에는 m,n (m은 사과개수, n은 오렌지개수)
4. 각 사과가 점 a에서 떨어지는 각각의 거리를 나타내는 공백으로 구분 된 정수
5. 각 오렌지가 점 b에서 떨어지는 각각의 거리를 나타내는 공백으로 구분 된 정수 

Output 설명

1. 샘 집에서 떨어지는 사과 수를 인쇄
2. 샘 집에서 덜어지는 오렌지 수를 인쇄

샘의 집 주위에 떨어지는 사과와 오렌지 개수를 구하는 문제

Sample Input
```
7 11
5 15
3 2
-2 2 1
5 -6
```

Sample Output
```
1
1
```

- 첫번째 사과 위치 : 5-2=3
- 두번째 사과 위치 : 5+2=7
- 세번째 사과 위치 : 5+1=6
- 첫번재 오렌지 위치 : 15+5=20
- 두번째 오렌지 위치 : 15-6=9
- 샘의 집 위치는 7~11이고 그 사이에 존재하는 사과 개수는 1개, 오렌지 개수는 1 개이다.


## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static void Main(String[] args) {
        string[] tokens_s = Console.ReadLine().Split(' ');
        int s = Convert.ToInt32(tokens_s[0]);
        int t = Convert.ToInt32(tokens_s[1]);
        string[] tokens_a = Console.ReadLine().Split(' ');
        int a = Convert.ToInt32(tokens_a[0]);
        int b = Convert.ToInt32(tokens_a[1]);
        string[] tokens_m = Console.ReadLine().Split(' ');
        int m = Convert.ToInt32(tokens_m[0]);
        int n = Convert.ToInt32(tokens_m[1]);
        string[] apple_temp = Console.ReadLine().Split(' ');
        int[] apple = Array.ConvertAll(apple_temp,Int32.Parse);
        string[] orange_temp = Console.ReadLine().Split(' ');
        int[] orange = Array.ConvertAll(orange_temp,Int32.Parse);
        
        int appleSum = 0;
        int orangeSum = 0;
        
        for (int i = 0; i < m; i++)
        {
            int aPos = apple[i] + a;
            if (aPos >= s && aPos <= t)
            {
                appleSum++;
            }
        }
        
        for (int i = 0; i < n; i++)
        {
           int oPos = orange[i] + b;
           if (oPos >= s && oPos <= t)
           {
               orangeSum++;
           }
        }
        
        Console.Write("{0}\n{1}", appleSum, orangeSum);
    }
}

```

## vatsalchanana의 답안

```cpp
#include <cmath>
#include <cstdio>
#include <vector>
#include <iostream>
#include <algorithm>
using namespace std;


int main() {
    int s, t, a, b, n, m, d, ans1=0, ans2=0;
    cin >> s >> t >> a >> b >> m >> n;

    for(int i=0;i<m; i++) {
        cin>>d;
        d = a+d;
        if(d>=s && d<=t)
            ans1++;
    }
    for(int i=0;i<n; i++) {
        cin>>d;
        d = b+d;
        if(d>=s && d<=t)
            ans2++;
    }
    cout << ans1 << endl;
    cout << ans2 << endl; 
    return 0;
}
```

## 느낀점

특정 범위 안에 수를 구하는 문제. if문과 && 연산자만 사용하면 풀 수 있는 문제이다. 문제 자체는 어려운게 없다.

문제 자체에 대한 설명이 문제의 난이도에 비해서 좀 길지 않나 싶다.
