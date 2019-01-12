---
layout: post
title: "[Codewars #28] Write Number in Expanded Form (6kyu)"
excerpt: "[Codewars #28] Write Number in Expanded Form (6kyu) 문제 풀이"
date: 2019-01-12 23:54:00 +0900
tags: [codewars, kata]
---

## Instructions

Write Number in Expanded Form
You will be given a number and you will need to return it as a string in Expanded Form. For example:

```
Kata.ExpandedForm(12); # Should return "10 + 2"
Kata.ExpandedForm(42); # Should return "40 + 2"
Kata.ExpandedForm(70304); # Should return "70000 + 300 + 4"
```

NOTE: All numbers will be whole numbers greater than 0.

If you liked this kata, check out part 2!!

## My Solution

```csharp
using System;
using System.Text;

public static class Kata
{
    public static string ExpandedForm(long num)
    {
      // long to string
      string strNum = "" + num;
      StringBuilder builder = new StringBuilder();
      for (int i = 0; i < strNum.Length; i++)
      {
        if (strNum[i] > '0')
        {
          if (builder.Length != 0)
          {
            builder.Append(" + ");
          }

          // char to string to int
          int n = int.Parse(strNum[i].ToString());
          double digit = strNum.Length - i - 1;
          digit = Math.Pow(10, digit);

          string strResult = "" + (n * digit);
          builder.Append(strResult);
        }
      }
      return builder.ToString();
    }
}
```

항상 이런 자릿수 문제가 나오면 long형을 string으로 바꾸어 준다음에 풀면 쉽게 풀 수 있다.

builder를 사용하여 풀었고, 저번에 문제를 풀다 보니 맨 처음에만 검사를 해서 기호를 넣어주면 쉽게 해결이 되는 부분이 있어서 그게 생각나서 그렇게 풀어 봤다.

[[Codewars #13] Help the bookseller (6kyu) Best Practices 2](({% post_url 2019-01-05-codewars-Help-the-bookseller %}))에서 사용한 방식

## Best Practices 1

```csharp
using System;
using System.Linq;

public static class Kata
{
    public static string ExpandedForm(long num)
    {
            var str = num.ToString();
            return String.Join(" + ", str
                .Select((x, i) => char.GetNumericValue(x) * Math.Pow(10, str.Length - i - 1))
                .Where(x => x > 0));
    }
}
```

Linq를 사용하여 간단히 해결!

string에 있는 0 보다 큰 모든 값을 전부 자릿수대로 곱해주고 그 값을 조인 시켜 구한다.

## Best Practices 2

```csharp
using System;
using System.Collections.Generic;

public static class Kata
{
  public static string ExpandedForm(long num)
  {
    Stack<long> parts = new Stack<long>();

    for (long m = 1, n = num; n > 0; n /= 10, m *= 10)
    {
      long digit = n % 10;
      if (digit > 0)
      {
        parts.Push(m * digit);
      }
    }

    return string.Join(" + ", parts);
  }
}
```

이거는 표 자체는 많이 받지 못한 풀이 방식인데.

stack을 사용하여 일단 자리수를 전부 구한다음에, string join으로 " + "를 연결하는게 신박한 방식인 것 같아서 실어 봤다.