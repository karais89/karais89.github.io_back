---
layout: post
title: "[HackerRank] Warmup Solve Me First"
description: "HackerRank Warmup Solve Me First 문제 풀이"
date: 2017-10-29 01:30:00 +0900
tags: [hackerrank]
---

## 문제 요약

stdin으로 읽은 두 정수의 합을 반환하는 함수를 만들어라.

## 내 소스

```
using System;
using System.Collections.Generic;
using System.IO;
class Solution {
    static int solveMeFirst(int a, int b) { 
      // Hint: Type return a+b; below  
      return a + b;
    }
    static void Main(String[] args) {
        int val1 = Convert.ToInt32(Console.ReadLine());
        int val2 = Convert.ToInt32(Console.ReadLine());
        int sum = solveMeFirst(val1,val2);
        Console.WriteLine(sum);
    }
}      
```

## 느낀점

워밍업 단계라 주석에 이미 답이 나와 있다.

익숙한 C#으로 작성했다.

테스트 케이스가 작성되어 테스트 통과 후 답을 제출하면 된다.