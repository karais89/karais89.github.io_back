---
layout: post
title: "[HackerRank #16] Implementation - Birthday Chocolate"
excerpt: "HackerRank Implementation Birthday Chocolate 문제 풀이"
date: 2017-11-12 17:00:00 +0900
tags: [hackerrank]
---

## 문제 요약

릴리는 초콜렛 n 줄을 가지고 있다.

론의 생일이라 릴리는 자신의 초콜릿 바를 일부분 줄려고 한다.

생일의 월은 m일은 d라고 했을시 m에 연속된 숫자의 합이 d와 일치하는 숫자의 카운터 만큼 줄려고 한다.

Sample Input 0
```
5
1 2 1 3 2 
3 2
```

Sample Output 0
```
2
```

2번 연속해서 나오는 수의 합이 3인 숫자.

1+2=3, 2+1=3 이므로 2개이다.


Sample Input 1
```
6
1 1 1 1 1 1
3 2
```

Sample Output 1
```
0
```

2번 연속해서 나온 숫자의 합이 6인 것은 없다.

Sample Input 2
```
1
4
4 1
```

Sample Output 2
```
1
```


## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int solve(int n, int[] s, int d, int m){
        // Complete this function
        int count = 0;
        for (int i = 0; i < n; i++)
        {
            int sum = 0;
        
            for (int j = i; j < i + m && j < n; j++)
            {
                sum += s[j];
            }
            
            if (sum == d)
            {
                count++;
            }
        }        
        return count;
    }

    static void Main(String[] args) {
        int n = Convert.ToInt32(Console.ReadLine());
        string[] s_temp = Console.ReadLine().Split(' ');
        int[] s = Array.ConvertAll(s_temp,Int32.Parse);
        string[] tokens_d = Console.ReadLine().Split(' ');
        int d = Convert.ToInt32(tokens_d[0]);
        int m = Convert.ToInt32(tokens_d[1]);
        int result = solve(n, s, d, m);
        Console.WriteLine(result);
    }
}
```

## adititayal9의 답안

```java
import java.util.*;

public class Solution {
    
    public static int getNumberOfWays(int n, int d, int m, int[] sum) {
        // Modify array to make each 'i' contain the running sum of prior elements
        for (int i = 1; i < n; i++) {
            sum[i] += sum[i - 1];
        }
        
        // Set number of ways counter
        // If there are >= 'm' squares AND the first possible piece has sum = 'd', 1
        // Else, 0
        int numberOfWays = (m <= n && sum[m - 1] == d) ? 1 : 0;
        
        // Check the sums for pieces ending at index 'm' through 'n - 1'
        for (int i = m; i < n; i++) {
            // If the sum of the piece is equal to 'd'
            if (sum[i] - sum[i - m] == d) {
                // Increment ways counter
                numberOfWays++;
            }
        }
        
        return numberOfWays;
    }

    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int[] squares = new int[n];
        for(int squares_i=0; squares_i < n; squares_i++){
            squares[squares_i] = in.nextInt();
        }
        int d = in.nextInt();
        int m = in.nextInt();
        in.close();

        System.out.println(getNumberOfWays(n, d, m, squares));
    }
}
```

## Prince_sai의 답안

```c
int getWays(int n, int* squares, int d, int m){
    // Complete this function
    int sum[105];
    int count=0;
    sum[0]=0;
    for(int i=0;i<n;i++)sum[i+1]=sum[i]+squares[i];
    for(int i=0;i<=n-m;i++){
        if(sum[i+m]-sum[i]==d){
            count++;
        }
    }
    return count;
}
```

## kcoddington0925의 답안

역시 linq를 사용하면 단 세줄로 문제를 풀 수 있다..

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution
{

    static int getWays(int[] squares, int d, int m)
    {
        int ways = 0;
        for (int i = 0; i < squares.Length - (m - 1); i++)
            if (squares.Skip(i).Take(m).Sum() == d) ways++;
        return ways;
    }

    static void Main(String[] args)
    {
        int n = Convert.ToInt32(Console.ReadLine());
        string[] s_temp = Console.ReadLine().Split(' ');
        int[] s = Array.ConvertAll(s_temp, Int32.Parse);
        string[] tokens_d = Console.ReadLine().Split(' ');
        int d = Convert.ToInt32(tokens_d[0]);
        int m = Convert.ToInt32(tokens_d[1]);
        int result = getWays(s, d, m);
        Console.WriteLine(result);
    }
}
```

## 느낀점

문제 자체를 이해하면 쉽게 풀 수 있는 문제이다.

사실 코딩을 하다 보면, for문 안에 여러개의 조건문을 넣는 경우가 그렇게 많지는 않았던 것 같은데..

일단 내가 짠 코드의 경우 O(n2) 이고 출제자의 경우는 O(n) 인듯 하다.

출제자는 그냥 배열을 이전 합계의 누적 합계가 포함되도록 배열을 수정하고, 거기서 답을 찾는 방식으로 진행 한 것 같다.