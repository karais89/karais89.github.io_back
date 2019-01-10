---
layout: post
title: "[Codewars #24] Alphabet war - airstrike - letters massacre (6kyu)"
excerpt: "[Codewars #24] Alphabet war - airstrike - letters massacre (6kyu) 문제 풀이"
date: 2019-01-10 22:23:00 +0900
tags: [codewars, kata]
---

## Instructions

There is a war and nobody knows - the alphabet war!
There are two groups of hostile letters. The tension between left side letters and right side letters was too high and the war began. The letters called airstrike to help them in war - dashes and dots are spreaded everywhere on the battlefield.

Task

Write a function that accepts fight string consists of only small letters and * which means a bomb drop place. Return who wins the fight after bombs are exploded. When the left side wins return Left side wins!, when the right side wins return Right side wins!, in other case return Let's fight again!.

The left side letters and their power:

```
 w - 4
 p - 3
 b - 2
 s - 1
```

The right side letters and their power:

```
m - 4
 q - 3
 d - 2
 z - 1
```

The other letters don't have power and are only victims.
The * bombs kills the adjacent letters ( i.e. aa*aa => a___a, **aa** => ______ );


Example

```
AlphabetWar("s*zz"); //=> Right side wins!
AlphabetWar("*zd*qm*wp*bs*"); //=> Let's fight again!
AlphabetWar("zzzz*s*"); //=> Right side wins!
AlphabetWar("www*www****z"); //=> Left side wins!
```

## My Solution

```csharp
 using System;
 using System.Collections.Generic;

 public class Kata
 {
    public static string AlphabetWar(string fight)
    {
      Dictionary<char, int> leftSideScores = new Dictionary<char, int>()
      {
        { 'w', 4 }, { 'p', 3 }, { 'b', 2 }, { 's', 1 }
      };

      Dictionary<char, int> rightSideScores = new Dictionary<char, int>()
      {
        { 'm', 4 }, { 'q', 3 }, { 'd', 2 }, { 'z', 1 }
      };

      // remove *
      char[] arr = fight.ToCharArray();
      for (int i = 0; i < arr.Length; i++)
      {
        if (arr[i] == '*')
        {
          arr[i] = '_';

          if (i-1 >= 0 && arr[i-1] != '*')
            arr[i-1] = '_';

          if (i+1 < arr.Length && arr[i+1] != '*')
            arr[i+1] = '_';
        }
      }

      int totalLeftScore = 0;
      int totalRightScore = 0;
      for (int i = 0; i < arr.Length; i++)
      {
        if (leftSideScores.ContainsKey(arr[i]))
          totalLeftScore += leftSideScores[arr[i]];
        else if (rightSideScores.ContainsKey(arr[i]))
          totalRightScore += rightSideScores[arr[i]];
      }


      if (totalLeftScore > totalRightScore)
      {
        return "Left side wins!";
      }
      else if (totalLeftScore < totalRightScore)
      {
        return "Right side wins!";
      }

      return "Let's fight again!";
    }
 }
```


Left Side Score 및 Right Side Score의 경우 딕셔너리에 저장해서 사용.
String을 Char 배열로 만든 후에 *의 제거 처리를 하고, 점수를 구하는 식으로 해결


## Best Practices

```csharp
using System;
using System.Text;
using System.Text.RegularExpressions;
using System.Linq;
using System.Collections.Generic;
public class Kata
{
  public static string AlphabetWar(string fight)
  {
    Regex rgx = new Regex(@"\w{0,1}[*+]\w{0,1}");
    int leftPower = 0;
    int rightPower = 0;
    string afterBombing = rgx.Replace(fight, "");
    foreach(char c in afterBombing)
    {
      if(lefts.ContainsKey(c)) leftPower += lefts[c];
      else if(rights.ContainsKey(c)) rightPower += rights[c];
    }
    if (leftPower == rightPower) return "Let's fight again!";
    else return (leftPower > rightPower)? "Left side wins!":"Right side wins!";
  }
}
```

* 문자를 제거하는 부분을 정규 표현식을 사용하여 코드의 길이를 줄였다.
나머지 점수를 구하는 방식은 똑같다.

## Jekyll Blog error

아래 소스 부분에서 계속해서 에러가 난다. 왜 나는지도 모르겠음

```csharp
Dictionary<char,int> lefts = new Dictionary<char, int>(){{'w',4}, {'p',3}, {'b',2}, {'s',1}};
Dictionary<char,int> rights = new Dictionary<char,int>(){{'m',4}, {'q',3}, {'d',2}, {'z',1}};
```

에러 메일은 아래와 같다.

The page build failed for the `master` branch with the following error:

The variable `{{'w',4}` on line 120 in `_posts/2019-01-10-codewars-Alphabet-war-airstrike-letters-massacre.markdown` was not properly closed with `}}`. For more information, see https://help.github.com/articles/page-build-failed-tag-not-properly-terminated/.

For information on troubleshooting Jekyll see:

  https://help.github.com/articles/troubleshooting-jekyll-builds

If you have any questions you can contact us by replying to this email.