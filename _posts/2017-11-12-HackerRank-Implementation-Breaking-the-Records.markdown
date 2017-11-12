---
layout: post
title: "[HackerRank #15] Implementation - Breaking the Records"
description: "HackerRank Implementation Breaking the Records 문제 풀이"
date: 2017-11-12 14:37:00 +0900
tags: [hackerrank]
---

## 문제 요약

마리아는 농구를 시즌에 n 게임을 치른다.

마리아는 프로로 뛰고 싶기 때문에, 그녀의 경기가 끝날때 마다 점수를 배열로 순차적으로 매깁니다.

그녀는 시즌 별로 최고 점수를 깬 횟수와 최저 점수를 깬 횟수를 기록 합니다.

첫번째 인자로는 시즌의 총 경기 횟수를 입력 받고, 나머지 인자로는 그 시즌의 점수를 입력 받습니다.

Sample Input 0
```
9
10 5 20 20 4 5 2 25 1
```

Sample Output 0
```
2 4
```

설명.

그녀는 인덱스 2(20점), 7(25점)에서 최고 점수 기록을 깨고,
인덱스 1(5점), 4(4점), 6(2점), 8(1점) 에서 최저 점수 기록을 깻습니다.

Sample Input 0
```
10
3 4 21 36 10 28 35 5 24 42
```

Sample Output 0
```
4 0
```

그녀는 최고 기록을 4번이나 세웠고, 시즌에서 첫번째 기록에 비교해서 최악의 점수를 깬 적은 없습니다.


## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int[] getRecord(int[] s){
        // Complete this function
        int score = s[0];
        int bestScore = s[0];
        int worstScore = s[0];
        
        int bestCount = 0;
        int worstCount = 0;
        
        for (int i = 1; i < s.Length; i++)
        {
            if (s[i] > bestScore)
            {
                bestCount++;
                bestScore = s[i];
            }
            
            if (s[i] < worstScore)
            {
                worstCount++;
                worstScore = s[i];
            }            
        }
        
        return new int[] {bestCount, worstCount};
    }

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        string[] s_temp = Console.ReadLine().Split(' ');
        int[] s = Array.ConvertAll(s_temp,Int32.Parse);
        int[] result = getRecord(s);
        Console.WriteLine(String.Join(" ", result));
    }
}
```

## StefanK의 답안

```java
import java.io.*;
import java.util.*;
import java.text.*;
import java.math.*;
import java.util.regex.*;

public class Solution {

    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int[] score = new int[n];
        for(int score_i=0; score_i < n; score_i++){
            score[score_i] = in.nextInt();
        }
        int best = 0;
        int worst = 0;
        int bestCounter = 0;
        int worstCounter = 0;
        best = score[0];
        worst = score[0];
        for (int i = 1; i < score.length; i++) {
            if (score[i] < worst) {
                worst = score[i];
                worstCounter++;
            }
            if (score[i] > best) {
                best = score[i];
                bestCounter++;
            }
        }
        System.out.println(bestCounter + " " + worstCounter);
    }
}
```

## 느낀점

확인 해보니 답안과 똑같은 방식으로 풀음.

설명 자체를 하기 곤란한 난이도의 문제 였던 것 같다.