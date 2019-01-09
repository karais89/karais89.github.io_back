---
layout: post
title: "[Codewars #22] longest_palindrome (6kyu)"
excerpt: "[Codewars #22] longest_palindrome (6kyu) 문제 풀이"
date: 2019-01-09 14:06:00 +0900
tags: [codewars, kata]
---

## Instructions

Longest Palindrome

Find the length of the longest substring in the given string s that is the same in reverse.

As an example, if the input was “I like racecars that go fast”, the substring (racecar) length would be 7.

If the length of the input string is 0, the return value must be 0.

```
"a" -> 1
"aab" -> 2
"abcde" -> 1
"zzbaabcd" -> 4
"" -> 0
```

## My Solution

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Kata
{
  public static string ReverseString(string s)
  {
      char[] arr = s.ToCharArray();
      Array.Reverse(arr);
      return new string(arr);
  }

  public static int GetLongestPalindrome(string str)
  {
    //your code :)
    if (str == null || str.Length == 0)
    {
      return 0;
    }

    // 맨 처음 글자와 맨 마지막 글자가 일치하고, 점 점 차이를 좁히면서 모두 일치하면 회문이다?
    // 아니면 그냥 처음문자를 뒤집은것과 일치하면 회문?
    int longestVal = 0;

    // 회문 판단
    for (int i = 0; i < str.Length; i++)
    {
      char ch = str[i];
      for (int j = str.Length - 1; j >= i; j--)
      {
        if (ch != str[j])
        {
          continue;
        }

        string subStr = str.Substring(i, j-i+1);
        if (subStr == ReverseString(subStr))
        {
          if (longestVal < subStr.Length)
          {
            longestVal = subStr.Length;
          }
        }
      }
    }

    return longestVal;
  }
}
```

회문 판단 알고리즘

내 해결책은 먼저 첫번째 문자 부터 판단하여, 가장 마지막 문자에서부터 문자가 같은지 판단한다.
만약 문자가 같으면 회문이 될 가능성이 있는 문장이므로, ReverseString으로 문자를 뒤짚어도 같은 문자열인지 판단할 후 있다.

## Best Practices 1

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Kata
{
  public static int GetLongestPalindrome(string str)
  {
    //Case String is Null or Empty
    if (string.IsNullOrEmpty(str)) return 0;
    //Variable to store the length of the longest reversible word
    int longest = 0;
    //Array with all the subtrings in the string passed to the function
    string [] subStrs = GetSubstrings(str);
    //For each string in the array verify if it is reversible and if it is,
    //compare its length with longest
    foreach (string s in subStrs)
      longest = IsReversible(s) && s.Length > longest ? s.Length :
        longest;
    return longest;
  }

  //Function that verifies if a word is reversible
  public static bool IsReversible(string word)
  {
    char [] temp = word.ToCharArray();
    Array.Reverse(temp);
    string revWord = new string (temp);
    //Console.WriteLine(word + " " + revWord);
    return (revWord != word) ? false : true;
  }

  //Function that gets an array of all the substrings in a string
  public static string [] GetSubstrings(string word)
  {
    int count = 0;
    int length = word.Length;
    List<string> Substrs = new List<string>();
    for (int i = 0; i < word.Length; i++)
    {
      Substrs.Add(word.Substring(i));
      for (int j = 1; j < length; j++)
      {
        Substrs.Add(word.Substring(i,j));
      }
      length--;
    }
    return Substrs.ToArray();
  }
}
```

표 3개를 획득한 답안이다.

## Best Practices 2

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Kata
{
  public static bool IsPalindrome(string str)
  {
    var arr = str.ToCharArray();
    Array.Reverse(arr);
    var reversed = new string(arr);
    return str == reversed;
  }

  public static int GetLongestPalindrome(string str)
  {
    if (str == null)
      return 0;

    int max = 0;
    for (int i = 0; i < str.Length; ++i)
      for (int j = i; j < str.Length; ++j)
        if (IsPalindrome(str.Substring(i, j - i + 1)))
          max = Math.Max(max, j - i + 1);

    return max;
  }
}
```

이게 더 깔끔한 방법 인듯 하다.