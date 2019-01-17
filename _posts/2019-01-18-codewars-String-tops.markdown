---
layout: post
title: "[Codewars #33] String tops (6kyu)"
excerpt: "[Codewars #33] String tops (6kyu) 문제 풀이"
date: 2019-01-18 00:37:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](http://www.codewars.com/kata/59b7571bbf10a48c75000070/train/csharp)

Task
Write a function that accepts msg string and returns local tops of string from the highest to the lowest.
The string's tops are from displaying the string in the below way:

When the msg string is empty, return an empty string.
The input strings may be very long. Make sure your solution has good performance.
Check the test cases for more samples.

Note for C++
Do not post an issue in my solution without checking if your returned string doesn't have some invisible characters. You read most probably outside of msg string.

## My Solution

```csharp
using System;
using System.Text;
using System.Collections.Generic;

public static class Kata
{
  public static string Tops(string msg)
  {
    if (string.IsNullOrEmpty(msg))
    {
      return string.Empty;
    }

    int switchCount = 0;
    int addNum = 0;
    int goalNum = 0;
    List<int> topIndexs = new List<int>();
    for (int i = 0; i < msg.Length; i++)
    {
      if (i == goalNum)
      {
        // switchCount is odd top index
        if (switchCount % 2 == 1)
        {
          topIndexs.Add(i);
        }
        switchCount++;
        addNum++;
        goalNum += addNum;
      }
    }

    StringBuilder strBuilder = new StringBuilder();
    for (int i = topIndexs.Count - 1; i >= 0; i--)
    {
      strBuilder.Append(msg[topIndexs[i]]);
    }

    return strBuilder.ToString();
  }
}
```

공통적으로 증가하는 수가 존재할 것 같다는 생각을 했는데 (수열) 찾지를 못해서, 그냥 가장 큰 수의 인덱스를 저장한 후에 스트링을 뿌려주는 방식으로 처리 하였다.

수열을 찾지 못한 이유는 중간에 계산 실수가 있었다.


## Best Practices 1

```csharp
using System.Text;

public static class Kata
{
  public static string Tops(string msg)
  {
    StringBuilder result = new StringBuilder();

    for(int i = 1, x = 0; i < msg.Length; i += 5 + 4 * x, x++)
      result.Insert(0, msg[i]);

    return result.ToString();
  }
}
```

큰 수의 인덱스 배열 : 1 -> 6 -> 15 -> 28

차이 5, 9, 13

차이 : 5+(4*x) -> 5 -> 9 -> 13

으로 수열이 존재한다.

## Best Practices 2

```csharp
using System;
using System.Linq;
using System.Collections.Generic;


public static class Kata
{
    public static string Tops(string msg)
    {
        int jump = 1;
        Stack<char> result = new Stack<char>() { };
        for (int i = 1; i < msg.Length; i += jump)
        {
            if (jump % 2 != 0)
                result.Push(msg[i]);
            jump += 1;
        }
        return String.Join("", result);
    }

}
```

나와 생각한 방식은 거의 똑같은데, 훨씬 깔끔하게 처리 하였다.

일단 스트링을 뒤로 뿌려주는 부분을 스택을 통해 처리 하였다.
그리고 추가적으로 내 해결책에서 사용 하던 쓸데없는 변수도 사용하지 않았다.