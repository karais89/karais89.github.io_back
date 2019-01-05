---
layout: post
title: "[Codewars #13] Help the bookseller (6kyu)"
excerpt: "[Codewars #13] Help the bookseller (6kyu) 문제 풀이"
date: 2019-01-05 13:59:00 +0900
tags: [codewars, kata]
---

## Instructions

A bookseller has lots of books classified in 26 categories labeled A, B, ... Z. Each book has a code c of 3, 4, 5 or more capitals letters. The 1st letter of a code is the capital letter of the book category. In the bookseller's stocklist each code c is followed by a space and by a positive integer n (int n >= 0) which indicates the quantity of books of this code in stock.

For example an extract of one of the stocklists could be:

```
L = {"ABART 20", "CDXEF 50", "BKWRK 25", "BTSQZ 89", "DRTYM 60"}.
```

or

```
L = ["ABART 20", "CDXEF 50", "BKWRK 25", "BTSQZ 89", "DRTYM 60"] or ....
```

You will be given a stocklist (e.g. : L) and a list of categories in capital letters e.g :

```
  M = {"A", "B", "C", "W"}
```

or

```
  M = ["A", "B", "C", "W"] or ...
```

and your task is to find all the books of L with codes belonging to each category of M and to sum their quantity according to each category.

For the lists L and M of example you have to return the string (in Haskell/Clojure a list of pairs):

```
  (A : 20) - (B : 114) - (C : 50) - (W : 0)
```

where A, B, C, W are the categories, 20 is the sum of the unique book of category A, 114 the sum corresponding to "BKWRK" and "BTSQZ", 50 corresponding to "CDXEF" and 0 to category 'W' since there are no code beginning with W.

If L or M are empty return string is "" (Clojure should return an empty array instead).

Note:
In the result codes and their values are in the same order as in M.

## My Solution

```csharp
using System;
using System.Collections.Generic;
public class StockList {
    public static string stockSummary(String[] lstOfArt, String[] lstOf1stLetter) {
      if (lstOfArt == null || lstOfArt.Length == 0 || lstOf1stLetter == null || lstOf1stLetter.Length == 0) 
      {
        return string.Empty;
      }
      Dictionary<string, int> letterDic = new Dictionary<string, int>();
      for (int i = 0; i < lstOf1stLetter.Length; i++)
      {
        for (int j = 0; j < lstOfArt.Length; j++)
        {
          if (lstOfArt[j].Substring(0, 1) == lstOf1stLetter[i])
          {
            if (letterDic.ContainsKey(lstOf1stLetter[i]))
            {
              letterDic[lstOf1stLetter[i]] += int.Parse(lstOfArt[j].Split()[1]);
            }
            else
            {
              letterDic[lstOf1stLetter[i]] = int.Parse(lstOfArt[j].Split()[1]);
            }
          }
        }
      }
      string summary = string.Empty;
      for (int i = 0; i < lstOf1stLetter.Length; i++)
      {
        if (letterDic.ContainsKey(lstOf1stLetter[i]))
        {
          summary += $"({lstOf1stLetter[i]} : {letterDic[lstOf1stLetter[i]]}) - ";
        }
        else
        {
          summary += $"({lstOf1stLetter[i]} : 0) - ";
        }
      }
      summary = summary.Substring(0, summary.Length - 3);
      return summary;
    }
}
```

딕셔너리를 사용하여 합을 저장해서 풀어야지 라는 생각을 했다가 쓸데 없이 복잡해 진 것 같다.

생각해보니 굳이 딕셔너리를 안써도 되는 문제 였다.. 

" - " 기호를 제거하는 코드도 쓸데없이 지저분하다.

## Best Practices 1

```csharp
using System.Linq;

public class StockList {
  public static string stockSummary(string[] lstOfArt, string[] lstOf1stLetter) {
    if (!lstOfArt.Any()) return "";
    return string.Join(" - ",
      lstOf1stLetter.Select(c => string.Format("({0} : {1})", c,
        lstOfArt.Where(a => a[0] == c[0]).Sum(a => int.Parse(a.Split(' ')[1]))))); 
  }
}
```

Linq를 사용하면 언제나 코드가 짧아진다.

## Best Practices 2

```csharp
using System;
public class StockList {
    public static string stockSummary(String[] lstOfArt, String[] lstOf1stLetter) {
        if (lstOfArt.Length == 0) {
            return "";
        }
        string result = "";
        foreach (string m in lstOf1stLetter) {
            int tot = 0;
            foreach (string l in lstOfArt) {
                if (l[0] == m[0]) {
                    tot += int.Parse(l.Split(' ')[1]);
                }
            }
            if (!String.IsNullOrEmpty(result)) {
                result += " - ";
            }
            result += "(" + m + " : " + tot + ")";
        }
        return result;
    }
}
```

이렇게 풀면 되는 거였는데.. 너무 돌아간듯.
" - " 붙이는 방식은 다시 생각 해보자.