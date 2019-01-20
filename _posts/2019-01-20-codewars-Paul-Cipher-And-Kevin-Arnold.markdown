---
layout: post
title: "[Codewars #39] Paul Cipher & Kevin Arnold (5kyu)"
excerpt: "[Codewars #39] Paul Cipher & Kevin Arnold (5kyu) 문제 풀이"
date: 2019-01-20 22:38:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/57c4ed873189a5bea00007e6/train/csharp)

We need to encode a message.

Only alpha characters will be encoded. Non-alpha characters will be copied. So, the message " 0123. " would be encoded to " 0123. ".

For alpha characters, we will follow these rulse:

```
1. All alpha characters will be treated as upper case
2. The first alpha character will not change (except for switching to upper case).
3. All subsequent alpha characters will be shifted toward 'Z' by the
    alphabetical position of the previous alpha character.
    (wrap back to 'A' if 'Z' is passed)
```

For example: "He1lo" would be encoded as follows:
```
H -> H (first alpha character does not change)
e -> M (H is the previous alpha character, and is the 8th letter in the alphabet. E + 8 = M)
1 -> 1 (non alpha characters do not change)
l -> Q (E is the previous alpha character, and is the 5th letter in the alphabet. L + 5 = Q)
o -> A (L is the previous alpha character, and is the 12th letter in the alphabet. O + 12 = A)
```

So, "He1lo" would be encoded to "HM1QA"

Write two functions. One to encode and one to decode. (Decoding "HM1QA" should yield "HE1LO")

For both functions, empty strings and null strings should return empty strings.

## My Solution

```csharp
using System;
using System.Text;

namespace Kata
{
  public class PaulCipher
  {
    public static int GetCharOrder(char ch)
    {
      return (ch - 'A') + 1;
    }

    public static char MoveCharOrder(char ch, int order)
    {
      char moveCh = (char)(ch + order);
      if (moveCh > 'Z')
      {
        int reOrder = moveCh - 'Z';
        return (char)('A' + reOrder - 1);
      }

      if (moveCh < 'A')
      {
        int reOrder = 'A' - moveCh;
        return (char)('Z' - reOrder + 1);
      }

      return moveCh;
    }

    public static string Encode(string input)
    {
      if (string.IsNullOrEmpty(input))
      {
        return string.Empty;
      }

      StringBuilder builder = new StringBuilder();
      string upStr = input.ToUpper();
      char prevCh = (char)0;
      for (int i = 0; i < upStr.Length; i++)
      {
        char ch = upStr[i];
        if (char.IsLetter(ch))
        {
          if (prevCh != (char)0)
          {
            int order = GetCharOrder(prevCh);
            prevCh = ch;
            ch = MoveCharOrder(ch, order);
          }
          else
          {
            prevCh = ch;
          }
        }
        builder.Append(ch);
      }
      return builder.ToString();
    }

    public static string Decode(string input)
    {
      if (string.IsNullOrEmpty(input))
      {
        return string.Empty;
      }

      StringBuilder builder = new StringBuilder();
      string upStr = input.ToUpper();
      char prevCh = (char)0;
      for (int i = 0; i < upStr.Length; i++)
      {
        char ch = upStr[i];
        if (char.IsLetter(ch))
        {
          if (prevCh != (char)0)
          {
            int order = GetCharOrder(prevCh);
            ch = MoveCharOrder(ch, -order);
          }

          prevCh = ch;
        }
        builder.Append(ch);
      }
      return builder.ToString();
    }
  }
}

```

- 알파벳 문자만 인코딩 되고, 나머지는 그대로 복사 된다.
- 모든 알파벳은 대문자로 처리 된다.
- 첫번째 알파벳은 바뀌지 않는다 (대문자로 처리되는것을 제외하고)
- 변경되는 알파벳은 이전 알파벳의 순서 만큼 더해줘서 변경된다.
- 해당 기능을 가지는 디코더와 인코더를 만들어라.

하나의 함수 안에 처리를 하려다가, 그냥 제출 버튼을 눌렀다.
이전 문자 저장하는 순서가 살짝 달라서 조금 애먹었다.

## Best Practices

```csharp
namespace Kata
{
  public class PaulCipher
  {
    static string Process(string input, int direction)
    {
      if (string.IsNullOrEmpty(input)) return "";
      var cc = input.ToUpper().ToCharArray();
      var prevA = (char)0;
      for (var i = 0; i < cc.Length; i++)
      {
        if (!char.IsLetter(cc[i])) continue;
        if (prevA == 0) prevA = cc[i];
        else
        {
          var c = cc[i] + direction*(prevA - 64);
          if (c > 'Z' || c < 'A') c = c + -direction*('Z' - 64);
          prevA = direction > 0 ? cc[i] : (char)c;
          cc[i] = (char)c;
        }
      }
      return new string(cc);
    }

    public static string Encode(string input)
    {
      return Process(input, 1);
    }

    public static string Decode(string input)
    {
      return Process(input, -1);
    }
  }
}
```

이 해결책에서는 함수 하나에 처리 하였다.
문자 저장하는 순서도 디코더에서는 변경되기 전 문자를 저장 인코더에서는 변경된 문자를 저장한다.