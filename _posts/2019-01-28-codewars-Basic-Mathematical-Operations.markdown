---
layout: post
title: "[Codewars #60] Basic Mathematical Operations (8kyu)"
excerpt: "[Codewars #60] Basic Mathematical Operations (8kyu) 문제 풀이"
date: 2019-01-28 09:28:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/basic-mathematical-operations/train/csharp)

Your task is to create a function that does four basic mathematical operations.

The function should take three arguments - operation(string/char), value1(number), value2(number).
The function should return result of numbers after applying the chosen operation.

Examples:
```
basicOp('+', 4, 7) // Output: 11
basicOp('-', 15, 18) // Output: -3
basicOp('*', 5, 5) // Output: 25
basicOp('/', 49, 7) // Output: 7
```

## My Solution

```csharp
namespace Solution
{
  public static class Program
  {
    public static double basicOp(char operation, double value1, double value2)
    {
      switch (operation)
      {
        case '+': return value1 + value2;
        case '-': return value1 - value2;
        case '*': return value1 * value2;
        case '/': return value1 / value2;
      }

      return 0;
    }
  }
}
```

- 문자에 따라 각 연산을 해주면 된다.

## Best Practices

```csharp
namespace Solution
{
  public static class Program
  {
    public static double basicOp(char op, double val1, double val2)
    {
      switch(op){
        case '+': return val1+val2;
        case '-': return val1-val2;
        case '*': return val1*val2;
        case '/': return val1/val2;
        default:
           throw new System.ArgumentException("Unknown operation!", op.ToString());
      }
    }
  }
}
```

- 똑같지만, 여기서는 default일때 예외 발생 코드가 추가되어 있음.