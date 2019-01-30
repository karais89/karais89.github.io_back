---
layout: post
title: "[Codewars #62]  Exes and Ohs (7kyu)"
excerpt: "[Codewars #62]  Exes and Ohs (7kyu) 문제 풀이"
date: 2019-01-30 12:58:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/55908aad6620c066bc00002a/train/csharp)

Check to see if a string has the same amount of 'x's and 'o's. The method must return a boolean and be case insensitive. The string can contain any char.

Examples input/output:

```
XO("ooxx") => true
XO("xooxx") => false
XO("ooxXm") => true
XO("zpzpzpp") => true // when no 'x' and 'o' is present should return true
XO("zzoo") => false
```

## My Solution

```csharp
using System.Linq;
using System;
public static class Kata
{
  public static bool XO (string input)
  {
    string lowerStr = input.ToLower();
    int xCnt = 0, oCnt = 0;
    for (int i = 0; i < lowerStr.Length; i++)
    {
      if (lowerStr[i] == 'x')
      {
        xCnt++;
      }
      else if (lowerStr[i] == 'o')
      {
        oCnt++;
      }
    }
    return xCnt == oCnt;
  }
}
```

- 모두 소문자로 변경후 해당 문자열의 카운터를 검사한 후 증가.
- 마지막에 두 문자의 카운터 개수를 비교.
- 습관적으로 계속 for 문을 사용하는데 foreach 문을 사용 하는게 가독성 부분에서 +1 정도는 나은 부분일 듯 하다.

## Best Practices

```csharp
using System.Linq;
using System;
public static class Kata
{
  public static bool XO (string input)
  {
     return input.ToLower().Count(i => i == 'x') == input.ToLower().Count(i => i == 'o');
  }
}
```

- Linq는 이미 나올것 같았고. 이 Linq문은 그래도 보기가 편하긴 하다. (조건 자체가 복잡하지 않기 때문에)