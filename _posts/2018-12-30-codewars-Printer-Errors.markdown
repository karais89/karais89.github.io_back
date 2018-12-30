---
layout: post
title: "[Codewars #1] Printer Errors (7kyu)"
excerpt: "[Codewars #1] Printer Errors (7kyu) 문제 풀이"
date: 2018-12-30 22:32:00 +0900
tags: [codewars, kata]
---

## Instructions

In a factory a printer prints labels for boxes. For one kind of boxes the printer has to use colors which, for the sake of simplicity, are named with letters from a to m.

The colors used by the printer are recorded in a control string. For example a "good" control string would be aaabbbbhaijjjm meaning that the printer used three times color a, four times color b, one time color h then one time color a...

Sometimes there are problems: lack of colors, technical malfunction and a "bad" control string is produced e.g. aaaxbbbbyyhwawiwjjjwwm.

You have to write a function printer_error which given a string will output the error rate of the printer as a string representing a rational whose numerator is the number of errors and the denominator the length of the control string. Don't reduce this fraction to a simpler expression.

The string has a length greater or equal to one and contains only letters from a to z.

## My Solution

```csharp
using System;

public class Printer
{
    public static string PrinterError(String s)
    {
        // your code
        // a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z
        int totalLength = s.Length;
        int wrongCharNum = 0;
        for (int i = 0; i < totalLength; i++)
        {
            if (s[i] > 'm')
              wrongCharNum++;
        }
        return wrongCharNum.ToString() + "/" + totalLength.ToString();
    }
}
```

문자열 중에 m 이상의 문자는 잘못된 문자로 판단하여 더해주면 된다.
처음 문제라 간단한 문제가 나옴.


## Best Practices

```csharp
using System.Linq;

public class Printer
{
    public static string PrinterError(string s)
    {
        return s.Where(c => c > 'm').Count() + "/" + s.Length;
    }
}
```

Linq를 사용하여 해결. Linq 부분은 확실히 볼 필요성이 있는 듯.