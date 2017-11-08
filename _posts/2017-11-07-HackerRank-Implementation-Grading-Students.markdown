---
layout: post
title: "[HackerRank #11] Implementation - Grading Students"
description: "HackerRank Implementation Grading Students 문제 풀이"
date: 2017-11-07 21:22:00 +0900
tags: [hackerrank]
---

## 문제 요약

- 모든 학생들은 0에서 100까지의 등급을 받는다.
- 40 미만의 점수는 실패한 등급이다.

샘은 대학 교수이며 아래와 같이 등급을 매긴다.

- 등급간의 차이는 5씩 차이가 나며 등급과 3미만의 차이가 나면 다음 등급으로 반올림 한다.
- 만약 38 미만등급이면 결과가 실패한 등급이므로 반올림 하지 않는다.
- 예를들어 84점이면 85로 반올림 되고 29점이면 29이다.


## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int[] solve(int[] grades){
        // Complete this function
        int[] finalGrades = new int[grades.Length];
        for (int i = 0; i < grades.Length; i++)
        {
            // less than 38 fail grade
            if (grades[i] < 38)
            {
                finalGrades[i] = grades[i];
            }
            else                
            {
                int firstValue = grades[i] / 10 * 10;
                int secondValue = grades[i] % 10;
                int roundedValue = 0;
                if (secondValue > 5)
                {
                    roundedValue = firstValue + 10;
                }
                else
                {
                    roundedValue = firstValue + 5;
                }
                
                if (roundedValue - grades[i] < 3)
                {
                    finalGrades[i] = roundedValue;
                }
                else
                {
                    finalGrades[i] = grades[i];
                }
            }
        }
        return finalGrades;
    }

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        int[] grades = new int[n];
        for(int grades_i = 0; grades_i < n; grades_i++){
           grades[grades_i] = Convert.ToInt32(Console.ReadLine());   
        }
        int[] result = solve(grades);
        Console.WriteLine(String.Join("\n", result));
        

    }
}
```

## nabila_ahmed의 답안

```cpp
#include <bits/stdc++.h>
using namespace std;

void solution() {
     int n, x;
     cin>>n;
     for(int i=0; i<n; i++){
        cin>>x;
        if(x>=38 and x%5>=3){
            while(x%5!=0){
               x++;
            }
        }
        cout<<x<<endl;
     }
}

int main () {
    solution();
    return 0;
}
```

## enilaydagdemir의 답안

```csharp
static int solve(int grade){
        int result = grade;
        if (grade >= 38)
        {
            if ((5 - (grade % 5) + grade) - grade < 3)
                result = 5 - (grade % 5) + grade;
        }
        return result;
    }

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        int[] grades = new int[n];
        for(int grades_i = 0; grades_i < n; grades_i++){
           grades[grades_i] = Convert.ToInt32(Console.ReadLine()); 
           Console.WriteLine(solve(grades[grades_i]));
        }
    }
```

## 느낀점

내가 생각한거랑은 좀 다른 해결책을 제시함.

내 생각은

1. fianlValue 값을 구한다.
2. finalValue 에서 현재 값을 뺀다.
3. 그 수의 차이가 3미만이면 finalValue 값 (반올림 한다)
4. 그 수의 차이가 3이상이면 원래 값 (반올림 하지 않는다)

이런식으로 문제를 해결했다.

등급이 점수 5를 기준으로 나누어 지므로.

(5 - (grade % 5) + grade) 이런식으로도 다음 등급을 파악할 수 있다.

```
ex) 71

5 - (1) + 71 = 75

ex) 89

5 - (4) + 89 = 90
```