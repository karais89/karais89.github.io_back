---
layout: post
title: "[HackerRank #18] Implementation - Migratory Birds"
excerpt: "HackerRank Implementation Migratory Birds 문제 풀이"
date: 2017-11-15 00:55:00 +0900
tags: [hackerrank]
---

## 문제 요약

n 마리의 새 무리가 대륙을 날고 있습니다.

각 새는 유형이 있으며, 다른 유형은 ID 번호 1,2,3,4,5로 지정됩니다.

새 무리에서 가장 일반적으로 많이 보유하고 있는 무리의 유형을 출력하고, 만약 유형의 개수가 같으면, 유형의 개수의 숫자가 작은 것을 출력하세요.


Sample Input
```
6
1 4 4 4 5 3
```

Sample Output 0
```
4
```

설명

- Type 1: 1 bird
- Type 2: 0 birds
- Type 3: 1 bird
- Type 4: 3 birds
- Type 5: 1 bird

가장 높은 빈도에서 발생하는 유형 번호는 4 유형이므로 응답으로 4를 인쇄합니다.

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int migratoryBirds(int n, int[] ar) {
        // Complete this function
        int[] typeCount = new int[5];        
        for (int i = 0; i < n; i++)
        {
            typeCount[ar[i]-1]++;
        }
        
        int maxIndex = 0;
        int max = typeCount[0];
        for (int i = 1; i < typeCount.Length; i++)
        {
            if (typeCount[i] > max)
            {
                maxIndex = i;
                max = typeCount[i];
            }
        }        
        return maxIndex+1;
    }

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        string[] ar_temp = Console.ReadLine().Split(' ');
        int[] ar = Array.ConvertAll(ar_temp,Int32.Parse);
        int result = migratoryBirds(n, ar);
        Console.WriteLine(result);
    }
}
```

## StefanK의 답안

```java
import java.util.*;

public class Solution {

    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int[] types = new int[n];
        for(int arr_i=0; arr_i < n; arr_i++){
            types[arr_i] = in.nextInt();
        }
        int[] frequencies = new int[6]; //A
        for (int i = 0; i < types.length; i++) { //B
            frequencies[types[i]] += 1; //C
        }
        int mostCommon = 0;
        for (int i = 1; i < frequencies.length; i++) { //D
            if (frequencies[mostCommon] < frequencies[i]) {
                mostCommon = i; //E
            }
        }
        System.out.println(mostCommon);
    }
}
```

## 느낀점

배열을 생성하고 그 배열에 타입을 저장하면 끝나는 문제

내가 푼 방식에서 굳이 max 값을 저장할 필요가 없다는걸 봤다.

어차피 maxIndex 값을 가지고 있기 때문에 그 값으로 비교를 하면 된다.