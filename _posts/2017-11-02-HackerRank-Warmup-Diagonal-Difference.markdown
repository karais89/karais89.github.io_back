---
layout: post
title: "[HackerRank #5] Warmup - Diagonal Difference"
description: "HackerRank Warmup Diagonal Difference 문제 풀이"
date: 2017-11-02 14:04:00 +0900
tags: [hackerrank]
---

## 문제 요약

첫번째 input은 N x N 배열을 만들어 줄때 필요한 N의 개수

그리고 나머지 input은 그 배열의 각각의 인수의 값.

배열의 첫번째 대각선의 합(왼쪽 상단에서 시작해서 오른쪽 하단으로 끝나는)과 두번째 대각선(오른쪽 상단에서 시작해서 왼쪽 하단으로 끝나는)의 차를 절대값을 구하는 문제.

ex)

```
3
11 2 4
4 5 6
10 8 -12
```

- 첫번째 대각선의 합 : 11 + 5 - 12 = 4
- 두번째 대각선의 합 : 4 + 5 + 10 = 19
- 두 대각선의 차이의 절대값 : |4 - 19| = 15




## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        int[][] a = new int[n][];
        for(int a_i = 0; a_i < n; a_i++){
           string[] a_temp = Console.ReadLine().Split(' ');
           a[a_i] = Array.ConvertAll(a_temp,Int32.Parse);
        }
        
        // primary diagonal (0,0), (1,1), (2,2)
        int primarySum = 0;
        for (int i = 0; i < n; i++)
        {
            for (int j = 0; j < n; j++)
            {
                if (i == j)
                    primarySum += a[i][j];
            }
        }
        
        // secondary diagonal (2,0) (1,1) (0,2)
        int secondarySum = 0;
        for (int i = 0; i < n; i++)
        {
            for (int j = 0; j < n; j++)
            {
                if (i+j == n-1)
                    secondarySum += a[i][j];
            }
        }
        
        int diff = primarySum - secondarySum;               
        Console.Write(Math.Abs(diff));
    }
}
```

## vatsalchanana의 답안

```cpp
#include <iostream>

using namespace std;

int main() {
    int n;
    cin >> n;

    int arr[n][n];

    long long int d1=0; //First Diagonal
    long long int d2=0; //Second Diagonal

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            cin >> arr[i][j];
            if (i == j) d1 += arr[i][j];
            if (i == n - j - 1) d2 += arr[i][j];
        }
    }

    cout << abs(d1 - d2) << endl; //Absolute difference of the sums across the diagonals
    return 0;
}
```

## 느낀점

배열에서 첫 번째 대각선과 두번째 대각선의 인덱스 값을 구하는 방법을 생각하면 풀 수 있는 문제.

나의 경우에는 첫번째 대각선과 두번째 대각선을 각각 for문으로 돌려서 푸는 방식을 택하였다.

vatsalchanana의 경우는 for문 하나에서 대각선의 합을 구해버리는 방식으로 문제를 해결 하였다.