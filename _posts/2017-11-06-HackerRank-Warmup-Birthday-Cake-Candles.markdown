---
layout: post
title: "[HackerRank #9] Warmup - Birthday Cake Candles"
description: "HackerRank Warmup Birthday Cake Candles 문제 풀이"
date: 2017-11-06 13:21:00 +0900
tags: [hackerrank]
---

## 문제 요약

첫번째 인수로는 양초의 개수를 받고

나머지 인수는 그 양초의 높이를 각각 받는다.

콜린은 양초의 높이가 가장 큰 것들만 불을 끌 수 있다.

그녀가 성공적으로 날려버릴 수 있는 양초를 출력해라.

Sample Input
```
4
3 2 1 3
```

Sample Output
```
2
```


## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int birthdayCakeCandles(int n, int[] ar) {
        // Complete this function
        int max = ar[0];
        int maxCount = 0;
        
        for (int i = 0; i < n; i++)
        {
            if (max < ar[i])
            {
                max = ar[i];
            }
        }
        
        for (int i = 0; i < n; i++)
        {
            if (ar[i] == max)
            {
                maxCount++;
            }
        }
        
        return maxCount;
    }

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        string[] ar_temp = Console.ReadLine().Split(' ');
        int[] ar = Array.ConvertAll(ar_temp,Int32.Parse);
        int result = birthdayCakeCandles(n, ar);
        Console.WriteLine(result);
    }
}

```

## shashank21j의 답안

자바의 경우는 나와 거의 같은 식으로 풀었다.

```java
import java.util.*;

public class Solution {

    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        
        // the number of candles
        int n = scan.nextInt();
        
        // store the current maximum height of any candle, initialize to the minimum possible height of any candle
        int maxHeight = 1; 
        
        // count the number of candles having the maximum height
        int countMax = 0;
        
        for(int i = 0; i < n; i++) {
            int tmp = scan.nextInt();
            
            // if you read in a value larger than maxHeight, set new maxHeight
            if(tmp > maxHeight) {
                maxHeight = tmp;
                countMax = 1;
            }
            // if you read a value equal to the current value of maxHeight
            else if(tmp == maxHeight) {
            	// increment the count of candles having maximum height
                countMax++;
            }
        }
        scan.close();
        
        System.out.println(countMax);
    }
}
```

파이썬의 경우는 3줄로 표현이 가능하다.

```python
n = input()
arr = map(int, raw_input().split())
print arr.count(max(arr))
```

## 느낀점

문제 자체는 그냥 주어진 배열의 맥스값을 구하고, 맥스값의 개수를 구하는 문제이다.