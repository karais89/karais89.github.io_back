---
layout: post
title: "[HackerRank #7] Warmup - Staircase"
description: "HackerRank Warmup Staircase 문제 풀이"
date: 2017-11-04 09:20:00 +0900
tags: [hackerrank]
---

## 문제 요약

계단 문제.

n을 입력받으면 그 n만큼의 계단모양의 #을 출력해라.

Sample Input

```
6 
```
Sample Output

```
     #
    ##
   ###
  ####
 #####
######
```

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        
        for (int i = 0; i < n; i++)
        {
            // space
            for (int j = n-i-1; j > 0; j--)
            {
                Console.Write(' ');
            }
            
            // #
            for (int k = 0; k < i+1; k++)
            {
                Console.Write('#');
            }
            
            // new line
            Console.WriteLine();
        }
    }
}
```

## vatsalchanana의 답안

```cpp
#include<iostream>

using namespace std;

int main () {
    int height;
    cin >> height;

    for (int i = 1; i <= height; i++) {
        for (int j = 0; j < i; j++) {
            if(j==0) {		
                //Printing spaces 
                for(int t = 0; t < height - i; t++) cout << " ";
            }
            //Print hashes
            cout << "#";
        }
        cout << endl;
    }
    return 0;
}
```

### svecon 답안

이런식으로 한줄로 표현할 수도 있다.

```csharp
using System;
class Solution
{
    static void Main(String[] args)
    {
        int N = int.Parse(Console.ReadLine());
        for (int i = 0; i < N; i++)
            Console.WriteLine(new String('#', i + 1).PadLeft(N, ' '));
    }
}
```

## 느낀점

학부 시간에 c를 배울때 하는 * 출력하는 문제랑 똑같은 문제.