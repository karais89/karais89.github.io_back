---
layout: post
title: "[Codewars #45] Loading Kata: Rot13 (5kyu)"
excerpt: "[Codewars #45] Loading Kata: Rot13 (5kyu) 문제 풀이"
date: 2019-01-24 01:03:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/530e15517bc88ac656000716/train/csharp)

ROT13 is a simple letter substitution cipher that replaces a letter with the letter 13 letters after it in the alphabet. ROT13 is an example of the Caesar cipher.

Create a function that takes a string and returns the string ciphered with Rot13. If there are numbers or special characters included in the string, they should be returned as they are. Only letters from the latin/english alphabet should be shifted, like in the original Rot13 "implementation".

Please note that using "encode" in Python is considered cheating.

## My Solution

```csharp
using System;
using System.Text;

public class Kata
{
  public static string Rot13(string message)
  {
    // your code here
    StringBuilder builder = new StringBuilder();
    for (int i = 0; i < message.Length; i++)
    {
      char ch = message[i];
      if (char.IsLetter(ch))
      {
        char a = 'a';
        char z = 'z';

        if (char.IsUpper(ch))
        {
          a = 'A';
          z = 'Z';
        }
        ch += (char)13;
        if (ch > z)
        {
          ch = (char)(a + ch - z - 1);
        }
      }
      builder.Append((char)ch);
    }

    return builder.ToString();
  }
}
```

- 가끔씩 보면 문제 수준이 이해가 안가는 것들이 있다.
- 일단 13 더해주고, 넘어가면 자동으로 a 부터 시작하는 식으로 구현 함.


## Best Practices

```csharp
using System;

public class Kata
{
  public static string Rot13(string message)
  {
    string result = "";
            foreach (var s in message)
            {
                if ((s >= 'a' && s <= 'm') || (s >= 'A' && s <= 'M'))
                    result += Convert.ToChar((s + 13)).ToString();
                else if ((s >= 'n' && s <= 'z') || (s >= 'N' && s <= 'Z'))
                    result += Convert.ToChar((s - 13)).ToString();
                else result += s;
            }
            return result;
  }
}
```

- 13이 딱 절반이라 더하고 빼기를 하면서 순환을 시켜준것 같다.