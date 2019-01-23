---
layout: post
title: "[Codewars #46] Mixbonacci (5kyu)"
excerpt: "[Codewars #46] Mixbonacci (5kyu) 문제 풀이"
date: 2019-01-24 01:04:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/5811aef3acdf4dab5e000251/train/csharp)

This is the first of my "-nacci" series. If you like this kata, check out the zozonacci sequence too.

Task
1. Mix -nacci sequences using a given pattern p.
2. Return the first n elements of the mixed sequence.

Rules
1. The pattern p is given as a list of strings (or array of symbols in Ruby) using the pattern mapping below (e. g. ['fib', 'pad', 'pel'] means take the next fibonacci, then the next padovan, then the next pell number and so on).
2. When n is 0 or p is empty return an empty list.
3. If the length of p is more than n repeat the pattern.

Examples
```
            0 1 2 3 4
----------+------------------
fibonacci:| 0, 1, 1, 2, 3 ...
padovan: | 1, 0, 0, 1, 0 ...
pell: | 0, 1, 2, 5, 12 ...

pattern = ['fib', 'pad', 'pel']
n = 6
# ['fib', 'pad', 'pel', 'fib', 'pad', 'pel']
# result = [fibonacci(0), padovan(0), pell(0), fibonacci(1), padovan(1), pell(1)]
result = [0, 1, 0, 1, 0, 1]

pattern = ['fib', 'fib', 'pel']
n = 6
# ['fib', 'fib', 'pel', 'fib', 'fib', 'pel']
# result = [fibonacci(0), fibonacci(1), pell(0), fibonacci(2), fibonacci(3), pell(1)]
result = [0, 1, 0, 1, 2, 1]
```

Sequences
- fibonacci : 0, 1, 1, 2, 3 ...
- padovan: 1, 0, 0, 1, 0 ...
- jacobsthal: 0, 1, 1, 3, 5 ...
- pell: 0, 1, 2, 5, 12 ...
- tribonacci: 0, 0, 1, 1, 2 ...
- tetranacci: 0, 0, 0, 1, 1 ...

Pattern mapping
- 'fib' -> fibonacci
- 'pad' -> padovan
- 'jac' -> jacobstahl
- 'pel' -> pell
- 'tri' -> tribonacci
- 'tet' -> tetranacci

If you like this kata, check out the zozonacci sequence.

## My Solution

```csharp
using System;
using System.Numerics;
using System.Collections.Generic;

namespace Solution
{
  public static class Kata
  {
    // non-recursive
    // Dynamic Programming 
    public static BigInteger Fibonacci(int n)
    {
      if (n < 2)
      {
        return n;
      }
      
      BigInteger[] arr = new BigInteger[n + 1];
      arr[0] = 0;
      arr[1] = 1;
  
      for (int i = 2; i <= n; i++)
      {
        arr[i] = arr[i - 1] + arr[i - 2];
      }
      return arr[n];    
    }
    
    // a(n) = a(n-2) + a(n-3) with a(0)=1, a(1)=a(2)=0. 
    public static BigInteger Padovan(int n)
    {
      if (n == 0)
      {
        return 1;
      }
      
      if (n <= 2)
      {
        return 0;
      }
      
      BigInteger[] arr = new BigInteger[n + 1];
      arr[0] = 1;
      arr[1] = 0;
      arr[2] = 0;
  
      for (int i = 3; i <= n; i++)
      {
        arr[i] = arr[i - 2] + arr[i - 3];
      }
      return arr[n];      
    }
    
    // a(n) = a(n-1) + 2*a(n-2), with a(0) = 0, a(1) = 1. 
    public static BigInteger Jacobstahl(int n)
    {
      if (n < 2)
      {
        return n;
      }
      
      BigInteger[] arr = new BigInteger[n + 1];
      arr[0] = 0;
      arr[1] = 1;
  
      for (int i = 2; i <= n; i++)
      {
        arr[i] = arr[i - 1] + 2 * arr[i - 2];
      }
      return arr[n];   
    }
        
    // a(0) = 0, a(1) = 1; for n > 1, a(n) = 2*a(n-1) + a(n-2). 
    public static BigInteger Pell(int n)
    {    
      if (n < 2)
      {
        return n;
      }
      
      BigInteger[] arr = new BigInteger[n + 1];
      arr[0] = 0;
      arr[1] = 1;
  
      for (int i = 2; i <= n; i++)
      {
        arr[i] = 2 * arr[i - 1] + arr[i - 2];
      }
      return arr[n];  
    }
    
    // a(n) = a(n-1) + a(n-2) + a(n-3) with a(0)=a(1)=0, a(2)=1. 
    public static BigInteger Tribonacci(int n)
    {
      if (n == 0 || n == 1)
      {
        return 0;
      }
      
      if (n == 2)
      {
        return 1;
      }
      
      BigInteger[] arr = new BigInteger[n + 1];
      arr[0] = 0;
      arr[1] = 0;
      arr[2] = 1;
  
      for (int i = 3; i <= n; i++)
      {
        arr[i] = arr[i - 1] + arr[i - 2] + arr[i - 3];
      }
      return arr[n];   
    }
    
    // a(n) = a(n-1) + a(n-2) + a(n-3) + a(n-4) with a(0)=a(1)=a(2)=0, a(3)=1. 
    public static BigInteger Tetranacci(int n)
    {
      if (n < 3)
      {
        return 0;
      }
      
      if (n == 3)
      {
        return 1;
      }
      
      BigInteger[] arr = new BigInteger[n + 1];
      arr[0] = 0;
      arr[1] = 0;
      arr[2] = 0;
      arr[3] = 1;
      
      for (int i = 4; i <= n; i++)
      {
        arr[i] = arr[i - 1] + arr[i - 2] + arr[i - 3] + arr[i - 4];
      }
      return arr[n];   
    }
    
    public static void ShowConsole(string head, Func<int, BigInteger> func, int n)
    {      
      Console.Write(head + " : ");      
      for (int i = 0; i < n; i++)
      {
        Console.Write(" " + func(i));      
      }
      Console.WriteLine();    
    }
        
    public static BigInteger[] Mixbonacci(string[] pattern, int length)
    {
    /*
      ShowConsole("fib", Fibonacci, 10);
      ShowConsole("pad", Padovan, 10);
      ShowConsole("jac", Jacobstahl, 10);
      ShowConsole("pel", Pell, 10);
      ShowConsole("tri", Tribonacci, 10);
      ShowConsole("tet", Tetranacci, 10);
    */
      if (length == 0 || pattern == null || pattern.Length == 0)
      {
        return new BigInteger[] {};     
      }
    
      Dictionary<string, int> boancciCounts = new Dictionary<string, int>();
      boancciCounts["fib"] = 0;
      boancciCounts["pad"] = 0;
      boancciCounts["jac"] = 0;
      boancciCounts["pel"] = 0;
      boancciCounts["tri"] = 0;
      boancciCounts["tet"] = 0;
      
      Dictionary<string, Func<int, BigInteger>> boancciFuncs = new Dictionary<string, Func<int, BigInteger>>();
      boancciFuncs["fib"] = Fibonacci;
      boancciFuncs["pad"] = Padovan;
      boancciFuncs["jac"] = Jacobstahl;
      boancciFuncs["pel"] = Pell;
      boancciFuncs["tri"] = Tribonacci;
      boancciFuncs["tet"] = Tetranacci;
      
            
      BigInteger[] mixbonaccis = new BigInteger[length];             
      for (int i = 0; i < length; i++)
      {        
        string key = pattern[i % pattern.Length];     
        mixbonaccis[i] = boancciFuncs[key](boancciCounts[key]++);
      }
       
      return mixbonaccis;
    }
  }
}
```

- 여러가지 점화식이 있는 함수들로 수를 표현하면 되는 문제.
- 여러가지 규칙의 함수들이 있다.
- 사실 문제 자체는 재귀 함수를 사용하는 방식이 가장 풀기 쉬운 방식인데, 스택 문제와 퍼포먼스 문제 때문에 해당 방법은 사용하면 안되는 듯 하다.
- Func 까지 써가면서 품..
- 재귀함수가 아닌 다이나믹 프로그래밍 방식을 사용함 (배열 사용)
- 쓸데없이 길어지는 느낌이 없지 않아 있다.

## Best Practices

```csharp
using System.Numerics;
using System.Collections.Generic;
namespace Solution
{
  public static class Kata
  {
    private static readonly Dictionary<string, IEnumerable<BigInteger>> GeneratorMapping =
      new Dictionary<string, IEnumerable<BigInteger>>() {
      {"fib", FibonacciGenerator()},
      {"pad", PadovanGenerator()},
      {"jac", JacobstahlGenerator()},
      {"tet", TetranacciGenerator()},
      {"tri", TribonacciGenerator()},
      {"pel", PellGenerator()}
    };

    public static BigInteger[] Mixbonacci(string[] pattern, int length)
    {
      if (pattern.Length == 0 || length == 0)
      {
        return new BigInteger[] { };
      }

      var res = new List<BigInteger>() { };
      var gens = new Dictionary<string, IEnumerator<BigInteger>>();
      var patLength = pattern.Length;

      for (var i = 0; i < patLength; i++)
      {
        var v = pattern[i];
        gens[v] = GeneratorMapping[v].GetEnumerator();
      }

      for (var i = 0; i < length; i++)
      {
        var gen = gens[pattern[i % patLength]];
        gen.MoveNext();
        res.Add(gen.Current);
      }

      return res.ToArray();

    }

    public static IEnumerable<BigInteger> FibonacciGenerator()
    {
      var a = new BigInteger(0);
      var b = new BigInteger(1);
      BigInteger x;
      while (true)
      {
        yield return a;
        x = a;
        a = b;
        b = x + a;
      }
    }

    public static IEnumerable<BigInteger> PadovanGenerator()
    {
      var a = new BigInteger(1);
      var b = new BigInteger(0);
      var c = new BigInteger(0);
      BigInteger x;
      BigInteger y;
      while (true)
      {
        yield return a;
        x = a;
        y = b;
        a = b;
        b = c;
        c = x + y;
      }
    }

    public static IEnumerable<BigInteger> JacobstahlGenerator()
    {
      var a = new BigInteger(0);
      var b = new BigInteger(1);
      BigInteger x;
      while (true)
      {
        yield return a;
        x = a;
        a = b;
        b = 2 * x + b;
      }
    }


    public static IEnumerable<BigInteger> PellGenerator()
    {
      var a = new BigInteger(0);
      var b = new BigInteger(1);
      BigInteger x;
      while (true)
      {
        yield return a;
        x = a;
        a = b;
        b = x + 2 * b;
      }
    }

    public static IEnumerable<BigInteger> TribonacciGenerator()
    {
      var a = new BigInteger(0);
      var b = new BigInteger(0);
      var c = new BigInteger(1);
      BigInteger x, y, z;
      while (true)
      {
        yield return a;
        x = a; y = b; z = c;
        a = b; b = c;
        c = x + y + z;
      }
    }

    public static IEnumerable<BigInteger> TetranacciGenerator()
    {
      var a = new BigInteger(0);
      var b = new BigInteger(0);
      var c = new BigInteger(0);
      var d = new BigInteger(1);
      BigInteger x, y, z, j;
      while (true)
      {
        yield return a;
        x = a; y = b; z = c; j = d;
        a = b; b = c; c = d;
        d = x + y + z + j;
      }
    }
  }
}
```

- IEnumerable 특성을 이용해서 해결.