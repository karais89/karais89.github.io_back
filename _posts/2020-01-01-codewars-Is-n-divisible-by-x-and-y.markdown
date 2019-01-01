---
layout: post
title: "[Codewars #6] Is n divisible by x and y? (8kyu)"
excerpt: "[Codewars #6] Is n divisible by x and y? (8kyu) 문제 풀이"
date: 2019-01-01 23:23:00 +0900
tags: [codewars, kata]
---

## Instructions

Create a function isDivisible(n, x, y) that checks if a number n is divisible by two numbers x AND y. All inputs are positive, non-zero digits.

```
Example:
isDivisible(3,1,3)--> true because 3 is divisible by 1 and 3
isDivisible(12,2,6)--> true because 12 is divisible by 2 and 6
isDivisible(100,5,3)--> false because 100 is not divisible by 3
isDivisible(12,7,5)--> false because 12 is neither divisible by 7 nor 5
```

## My Solution

```csharp
public class DivisibleNb {
 public static bool isDivisible(long n, long x, long y) {
  // your code
    return n % x == 0 && n % y == 0;
 }
}

```

조건 자체가 모든 수는 0이 아니고, 자연수이기때문에 따로 조건 체크는 하지 않고, 두개의 수를 나누었을때 나머지가 0이 아닌 경우를 구하여 리턴 해 주었다.

## Best Practices

```csharp
public class DivisibleNb {
    public static bool isDivisible(long n, long x, long y) {
        return (x != 0 && y != 0 && n % x == 0 && n % y == 0);
    }
}
```

문제 해결 방법은 똑같은것 같고, 여기서는 x, y에 대한 0 검사를 해주어 Division by zero에 대한 처리를 해주었다. 

이 해결에 대한 의견으로 아래와 같은 의견이 달림.

```
All inputs are positive, non-zero digits!
x != 0 && y != 0 superfluous
```
