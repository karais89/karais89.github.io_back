---
layout: post
title: "[Codewars #38] Play with two Strings (5kyu)"
excerpt: "[Codewars #38] Play with two Strings (5kyu) 문제 풀이"
date: 2019-01-19 20:22:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/56c30ad8585d9ab99b000c54/train/csharp)

Your task is to Combine two Strings. But consider the rule...

By the way you don't have to check errors or incorrect input values, every thing is ok without bad tricks, only two input strings and as result one output string;-)...

And here's the rule:
Input Strings 'a' and 'b': For every character in string 'a' swap the casing of every occurance of the same character in string 'b'. Then do the same casing swap with the inputs reversed. Return a single string consisting of the changed version of 'a' followed by the changed version of 'b'. A char of 'a' is in 'b' regardless if it's in upper or lower case - see the testcases too.
I think that's all;-)...

Some easy examples:

```
Input: "abc" and "cde" => Output: "abCCde"
Input: "abab" and "bababa" => Output: "ABABbababa"
Input: "ab" and "aba" => Output: "aBABA"
```

There are some static tests at the beginning and many random tests if you submit your solution.

Hope you have fun:-)!

## My Solution

```csharp
namespace smile67Kata
{
    using System;
    using System.Text;

    public class Kata
    {
        public string SwapUpperOrLower(string a, string b)
        {
            StringBuilder aBuilder = new StringBuilder();
            for (int i = 0; i < a.Length; i++)
            {
              int equalCnt = 0;
              for (int j = 0; j < b.Length; j++)
              {
                if (char.ToUpper(a[i]) == char.ToUpper(b[j]))
                {
                  equalCnt++;
                }
              }

              char inChar = a[i];
              if (equalCnt % 2 != 0)
              {
                inChar = char.IsLower(inChar) ? char.ToUpper(inChar) : char.ToLower(inChar);
              }
              aBuilder.Append(inChar);
            }
            return aBuilder.ToString();
        }

        public string workOnStrings(string a, string b)
        {
            // a, b
            string newA = SwapUpperOrLower(a, b);

            // b, a
            string newB = SwapUpperOrLower(b, a);

            return newA+newB;
        }
    }
}
```

- a와 b에 대해서 문자열 a의 모든 문자에 대해 문자열 b에 있는 동일한 문자가 있을때 대소 문자를 바꾼다 스위칭 하면서 바꿔준다.
- b도 똑같은 작업을 진행한다.
- a+b를 합친후 리턴

## Best Practices

```csharp
namespace smile67Kata
{
    using System;
    using System.Linq;
    public class Kata
    {
        public char needswitch(string s,char c){
          return !(s.ToLower().Contains(char.ToLower(c))&&s.ToLower().Count(y=>y==char.ToLower(c))%2==1)
                 ? c : c>96 ? char.ToUpper(c) : char.ToLower(c);
        }
        public string workOnStrings(string a, string b){
          return string.Join("",a.Select(x=>needswitch(b,x))) + string.Join("",b.Select(x=>needswitch(a,x)));
        }
    }
}
```

- Linq 사용
- 아스키 코드 97 부터 소문자 a