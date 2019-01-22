---
layout: post
title: "[Codewars #41] Alphabet wars - nuclear strike (5kyu)"
excerpt: "[Codewars #41] Alphabet wars - nuclear strike (5kyu) 문제 풀이"
date: 2019-01-22 19:44:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/59437bd7d8c9438fb5000004/train/csharp)

Introduction

There is a war and nobody knows - the alphabet war!
The letters hide in their nuclear shelters. The nuclear strikes hit the battlefield and killed a lot of them.

Task

Write a function that accepts battlefield string and returns letters that survived the nuclear strike.

- The battlefield string consists of only small letters, #,[ and ].
- The nuclear shelter is represented by square brackets []. The letters inside the square brackets represent letters inside the shelter.
- The # means a place where nuclear strike hit the battlefield. If there is at least one # on the battlefield, all letters outside of shelter die. When there is no any # on the battlefield, all letters survive (but do not expect such scenario too often ;-P ).
- The shelters have some durability. When 2 or more # hit close to the shelter, the shelter is destroyed and all letters inside evaporate. The 'close to the shelter' means on the ground between the shelter and the next shelter (or beginning/end of battlefield). The below samples make it clear for you.

Example
```
abde[fgh]ijk => "abdefghijk" (all letters survive because there is no # )
ab#de[fgh]ijk => "fgh" (all letters outside die because there is a # )
ab#de[fgh]ij#k => "" (all letters dies, there are 2 # close to the shellter )
##abde[fgh]ijk => "" (all letters dies, there are 2 # close to the shellter )
##abde[fgh]ijk[mn]op => "mn" (letters from the second shelter survive, there is no # close)
#ab#de[fgh]ijk[mn]op => "mn" (letters from the second shelter survive, there is no # close)
#abde[fgh]i#jk[mn]op => "mn" (letters from the second shelter survive, there is only 1 # close)
[a]#[b]#[c] => "ac"
[a]#b#[c][d] => "d"
[a][b][c] => "abc"
##a[a]b[c]# => "c"
```

Alphabet war Collection
- Alphavet war
- Alphabet war - airstrike - letters massacre
- Alphabet wars - reinforces massacre
- Alphabet wars - nuclear strike
- Alphabet war - Wo lo loooooo priests join the war

## My Solution

```csharp
 using System;
 using System.Text;

 public class Kata
 {
    // battleFiledChar = '#', '[', ']'
    public static string RemoveBattleFieldChar(string b)
    {
        StringBuilder builder = new StringBuilder();
        for (int i = 0; i < b.Length; i++)
        {
          if (b[i] == '#' || b[i] == '[' || b[i] == ']')
          {
            continue;
          }
          builder.Append(b[i]);
        }
        return builder.ToString();
    }

    public static string DestoryBracketsOutLetters(string b)
    {
      StringBuilder rmBuilder = new StringBuilder();
      bool startBrackets = false;
      for (int i = 0; i < b.Length; i++)
      {
        switch (b[i])
        {
          case '#':
            rmBuilder.Append(b[i]);
            break;
          case '[':
            startBrackets = true;
            rmBuilder.Append(b[i]);
            break;
          case ']':
            startBrackets = false;
            rmBuilder.Append(b[i]);
            break;
          default:
            break;
        }

        if (startBrackets)
        {
          if (b[i] != '#' && b[i] != '[' && b[i] != ']')
          {
            rmBuilder.Append(b[i]);
          }
        }
      }

      Console.WriteLine("rmBuilder: " + rmBuilder);
      return rmBuilder.ToString();
    }

    public static string CheckSideSharpBrackets(string b)
    {
      StringBuilder strBuilder = new StringBuilder();
      int index = 0;
      while (true)
      {
        if (index >= b.Length)
        {
          break;
        }

        // startBreackets
        if (b[index] == '[')
        {
          // left sharp
          int sharpCnt = 0;
          int leftIdx = index;
          while (true)
          {
            if (leftIdx < 0 || b[leftIdx] == ']')
            {
              break;
            }

            if (b[leftIdx] == '#')
            {
              sharpCnt++;
            }

            leftIdx--;
          }

          // end brackets index
          int endIdx = index;
          while (true)
          {
            endIdx++;
            if (b.Length <= endIdx)
            {
              // parssing error
              break;
            }

            if (b[endIdx] == ']')
            {
              break;
            }
          }

          // rightSideCheck
          int rightIdx = endIdx;
          while (true)
          {
            if (rightIdx >= b.Length || b[rightIdx] == '[')
            {
              break;
            }

            if (b[rightIdx] == '#')
            {
              sharpCnt++;
            }

            rightIdx++;
          }

          if (sharpCnt >= 2)
          {
            index = endIdx;
          }
          Console.WriteLine("sharpCnt: " + sharpCnt);
        }

        strBuilder.Append(b[index]);
        index++;
      }
      Console.WriteLine("strBuilder: " + strBuilder);
      return RemoveBattleFieldChar(strBuilder.ToString());
    }

    public static string AlphabetWar(string b)
    {
      Console.WriteLine("b: " + b);

      int index = b.IndexOf('#');
      if (index == -1)
      {
        return RemoveBattleFieldChar(b);
      }

      // step 1 : destroy not brackets letters
      string str = DestoryBracketsOutLetters(b);

      // step 2 : brackets side check
      string ret = CheckSideSharpBrackets(str);
      return ret;
    }
}
```

- #이 하나도 없을시에는 #과 괄호 기호들을 제거하고 그대로 출력
- #이 하나라도 있을시 괄호 기호안의 알파벳을 제외하고 모두 제거
- 제거된 문자열로 괄호 양 옆을 조사한 후 #의 개수에 따라 로직 처리
- 문제를 보자마자 정규표현식을 써야겠다는 느낌은 옴.

## Best Practices 1

```csharp
using System;
using System.Text.RegularExpressions;
using System.Linq;
public class Kata
{
    public static string AlphabetWar(string b)
    => !b.Contains('#') ? Regex.Replace(b,@"[\[\]]","") :
       string.Concat(Regex.Matches(b, @"(?<=([a-z#]*))\[([a-z]+)\](?=([a-z#]*))").Cast<Match>().Where(g => (g.Groups[1].Value + g.Groups[3].Value).Count(c => c == '#') < 2).Select(g => g.Groups[2].Value));
}
```

- 정규표현식과 Linq의 조합으로 코드가 짧아진다.

## Best Practices 2

```csharp
using System.Linq;
using System.Text;
using System.Text.RegularExpressions;

public class Kata {
    public static string AlphabetWar( string battleField ) {
        const char strike = '#';
        var underAttack = battleField.Contains( strike );
        if ( !underAttack ) {
            return battleField.Replace( "[", string.Empty ).Replace( "]", string.Empty );
        }
        var survivors = new StringBuilder();
        var shelterAreaRegex = new Regex( @"(?'s1'[^\]]*)\[(?'s'[a-z]+)\](?'s2'[^\[]*)" );
        while ( shelterAreaRegex.IsMatch( battleField ) ) {
            var m = shelterAreaRegex.Match( battleField );
            var shelterPopulation = m.Groups [ "s" ].Value;
            var frontStrikesCount = m.Groups [ "s1" ].Value.Count( c => c == strike );
            var behindStrikesCount = m.Groups [ "s2" ].Value.Count( c => c == strike );
            if ( frontStrikesCount + behindStrikesCount < 2 ) {
                survivors.Append( shelterPopulation );
            }
            battleField = battleField.Replace( m.Value, m.Groups [ "s2" ].Value );
        }
        return survivors.ToString( );
    }
}
```

- 위 코드보다는 가독성이 더 나은듯.