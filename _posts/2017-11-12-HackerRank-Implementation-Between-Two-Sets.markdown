---
layout: post
title: "[HackerRank #14] Implementation - Between Two Sets"
excerpt: "HackerRank Implementation Between Two Sets 문제 풀이"
date: 2017-11-12 11:39:00 +0900
tags: [hackerrank]
---

## 문제 요약

Sample Input
```
2 3
2 4
16 32 96
```

Sample Output
```
3
```

```
a = {2,4}

b = {16, 32, 96}

x = 4:
a의 모든 요소는 4로 나누어짐
b의 모든 요소는 4로 나누어짐

x = 8:
a의 모든 요소는 8로 나누어짐
b의 모든 요소는 8로 나누어짐

x = 16:
a의 모든 요소는 16로 나누어짐
b의 모든 요소는 16로 나누어짐
```

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int GetTotalX(int[] a, int[] b) 
    {
        // Complete this function        
        int lcm = GetLCMByArr(a);
        int gcd = GetGCDByArr(b);
        
        
        int totalCount = 0;
        for (int i = lcm, j=2; i <= gcd; i=lcm*j, j++)
        {
            if (gcd % i == 0)
            {
                totalCount++;
            }
        }
        return totalCount;
    }

    // GCD = greatest common divisor
    static int GetGCD(int a, int b)
    {        
        int gcd = 1;
        int minVal = Math.Min(a, b);
        for (int i = 2; i <= minVal; i++)
        {
            if (a % i == 0 && b % i == 0)
            {
                gcd = i;
            }
        }
        return gcd;
    }
    
    // LCM = least common multiple
    static int GetLCM(int a, int b)
    {
        return a * b / GetGCD(a, b);
        /*
        int lcm = 1;
        int maxVal = Math.Max(a, b);
        for (int i = maxVal; ;i++)
        {
            if (i % a == 0 && i % b == 0)
            {
                lcm = i;
                break;
            }
        }
        return lcm;
        */
    }    
    
    static int GetGCDByArr(int[] arr)
    {
        int gcd = arr[0];        
        for (int i = 1; i < arr.Length; i++)
        {
            gcd = GetGCD(gcd, arr[i]);
        }
        return gcd;
    }
    
    static int GetLCMByArr(int[] arr)
    {
        int lcm = arr[0];
        for (int i = 1; i < arr.Length; i++)
        {
            lcm = GetLCM(lcm, arr[i]);
        }
        return lcm;
    }
    
    static void Main(String[] args) {
        string[] tokens_n = Console.ReadLine().Split(' ');
        int n = Convert.ToInt32(tokens_n[0]);
        int m = Convert.ToInt32(tokens_n[1]);
        string[] a_temp = Console.ReadLine().Split(' ');
        int[] a = Array.ConvertAll(a_temp,Int32.Parse);
        string[] b_temp = Console.ReadLine().Split(' ');
        int[] b = Array.ConvertAll(b_temp,Int32.Parse);
        int total = GetTotalX(a, b);
        Console.WriteLine(total);
    }
}
```

## zemen의 답안

```java
import java.util.*;

public class Solution {
    public static int gcd(int a, int b) {
        while (a > 0 && b > 0) {
            
            if (a >= b) {
                a = a % b;
            }
            else {
                b = b % a;
            }
        }

        return a + b;
    }

    public static int lcm(int a, int b) {
        return (a / gcd(a, b)) * b;
    }
    
    public static int getTotalX(int[] a, int[] b) {
        
        int multiple = 0;
        for(int i : b) {
            multiple = gcd(multiple, i);
        }
//        System.err.println("Multiple: " + multiple);
        
        int factor = 1;
        for(int i : a) {
            factor = lcm(factor, i);
            if (factor > multiple) {
                return 0;
            }
        }

        if (multiple % factor != 0) {
            return 0;
        }
//        System.err.println("Factor: " + factor);
        
        int value = multiple / factor;
        
        int max = Math.max(factor, value);
        int totalX = 1;
        
        for (int i = factor; i < multiple; i++) {
            if (multiple % i == 0 && i % factor == 0) {
                totalX++;
            }
        }
        return totalX;        
    }    
    
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        int n = scan.nextInt();
        int m = scan.nextInt();
        int[] a = new int[n];
        for (int i = 0; i < n; i++) {
            a[i] = scan.nextInt();
        }
        int[] b = new int[m];
        for (int i = 0; i < m; i++) {
            b[i] = scan.nextInt();
        }
        scan.close();
        
        int total = getTotalX(a, b);
        System.out.println(total);
    }
}
```

### t_tashasin의 답안

알고리즘 수행 속도 O(n log(n))의 해결방안:
1. a 배열의 LCM(최소공배수)을 찾아라. 
2. b 배열의 GCD(최대공약수)를 찾아라.
3. GCD를 균등하게 나누는 LCM의 배수를 세어라.

```java
public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        int m = in.nextInt();
        int[] a = new int[n];
        for(int a_i=0; a_i < n; a_i++){
            a[a_i] = in.nextInt();
        }
        int[] b = new int[m];
        for(int b_i=0; b_i < m; b_i++){
            b[b_i] = in.nextInt();
        }
        
        int f = lcm(a);
        int l = gcd(b);
        int count = 0;
        for(int i = f, j =2; i<=l; i=f*j,j++){
            if(l%i==0){ count++;}
        }
        System.out.println(count);
    }
    
    private static int gcd(int a, int b) {
        while (b > 0) {
            int temp = b;
            b = a % b; // % is remainder
            a = temp;
        }
        return a;
    }

    private static int gcd(int[] input) {
        int result = input[0];
        for (int i = 1; i < input.length; i++) {
            result = gcd(result, input[i]);
        }
        return result;
    }

    private static int lcm(int a, int b) {
        return a * (b / gcd(a, b));
    }

    private static int lcm(int[] input) {
        int result = input[0];
        for (int i = 1; i < input.length; i++) {
            result = lcm(result, input[i]);
        }
        return result;
    }
```

## 느낀점

먼저 처음에 문제 자체를 이해를 잘 못해서 해맨 상태에서, 최대 공약수 및 최소 공배수를 이용해서 문제를 푸는 것 자체는 파악을 했다.

하지만 다양한 테스트 케이스에서 통과를 하지 못하고, 결국 문제 자체는 힌트를 보고 풀었다.

결국엔 최대 공약수 및 최소 공배수의 알고리즘을 알면 풀 수 있는 문제라고 생각해서 문제를 풀었지만, 테스트 케이스에서 인자가 조금 많아지니, 알고리즘 자체의 성능이 문제가 되어 테스트 케이스에 통과하지 못했다.

최종적으로는 유클리드 호제법을 사용하여 문제를 풀어야 된다.

유클리드 호제법에 대해서는 나중에 한번 다시 한번 정리를 해봐야 될 것 같다.