---
layout: post
title: "[Codewars #47] Alphabet war - Wo lo loooooo priests join the war (5kyu)"
excerpt: "[Codewars #47] Alphabet war - Wo lo loooooo priests join the war (5kyu) 문제 풀이"
date: 2019-01-24 23:32:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/alphabet-war-wo-lo-loooooo-priests-join-the-war/train/csharp)

There is a war and nobody knows - the alphabet war!
There are two groups of hostile letters. The tension between left side letters and right side letters was too high and the war began. The letters have discovered a new unit - a priest with Wo lo looooooo power.

Task
Write a function that accepts fight string consists of only small letters and return who wins the fight. When the left side wins return Left side wins!, when the right side wins return Right side wins!, in other case return Let's fight again!.

The left side letters and their power:
```
 w - 4
 p - 3
 b - 2
 s - 1
 t - 0 (but it's priest with Wo lo loooooooo power)
```

The right side letters and their power:
```
 m - 4
 q - 3
 d - 2
 z - 1
 j - 0 (but it's priest with Wo lo loooooooo power)
```

The other letters don't have power and are only victims.
The priest units t and j change the adjacent letters from hostile letters to friendly letters with the same power.
```
mtq => wtp
wjs => mjz
```
A letter with adjacent letters j and t is not converted i.e.:
```
tmj => tmj
jzt => jzt
```
The priests (j and t) do not convert the other priests ( jt => jt).

Example
```
AlphabetWar("z") //=> "z" => "Right side wins!"
AlphabetWar("tz") //=> "ts" => "Left side wins!"
AlphabetWar("jz") //=> "jz" => "Right side wins!"
AlphabetWar("zt") //=> "st" => "Left side wins!"
AlphabetWar("azt") //=> "ast" => "Left side wins!"
AlphabetWar("tzj") //=> "tzj" => "Right side wins!"
```

Alphabet war Collection
- Alphabet war
- Alphabet war - airstrike - letters massacre
- Alphabet wars - reinforces massacre
- Alphabet wars - nuclear strike
- Alphabet war - Wo lo loooooo priests join the war

## My Solution

```csharp
 using System;
 using System.Text;
 using System.Collections.Generic;

 public class Kata
 {
    public static readonly Dictionary<char, int> leftSideLetters = new Dictionary<char, int>()
    {
        { 'w', 4 }, { 'p', 3 }, { 'b', 2 }, { 's', 1 }
    };

    public static readonly Dictionary<char, int> rightSideLetters = new Dictionary<char, int>()
    {
        { 'm', 4 }, { 'q', 3 }, { 'd', 2 }, { 'z', 1 }
    };

    public static char RightToLeftLetter(char ch)
    {
      if (rightSideLetters.ContainsKey(ch))
      {
        int score = rightSideLetters[ch];
        foreach (var left in leftSideLetters)
        {
          if (left.Value == score)
          {
            return left.Key;
          }
        }
      }

      return ch;
    }

    public static char LeftToRightLetter(char ch)
    {
      if (leftSideLetters.ContainsKey(ch))
      {
        int score = leftSideLetters[ch];
        foreach (var right in rightSideLetters)
        {
          if (right.Value == score)
          {
            return right.Key;
          }
        }
      }

      return ch;
    }

    public static string AlphabetWar(string fight)
    {
        int leftScore = 0;
        int rightScore = 0;

        //Console.WriteLine("fight: " + fight);

        StringBuilder convFight = new StringBuilder(fight);
        // priest char check
        for (int i = 0; i < convFight.Length; i++)
        {
          // left side priest
          if (convFight[i] == 't')
          {
            bool isTwoLeftPriest = (i - 2 >= 0 && convFight[i - 2] == 'j');
            if (!isTwoLeftPriest)
            {
              // left alpha change
              if (i - 1 >= 0)
              {
                convFight[i - 1] = RightToLeftLetter(convFight[i - 1]);
              }
            }

            bool isTwoRightPriest = (i + 2 <= convFight.Length - 1 && convFight[i + 2] == 'j');
            if (!isTwoRightPriest)
            {
              // right alpha change
              if (i + 1 <= convFight.Length - 1)
              {
                convFight[i + 1] = RightToLeftLetter(convFight[i + 1]);
              }
            }
          }

          // right side priest
          if (convFight[i] == 'j')
          {
            bool isTwoLeftPriest = (i - 2 >= 0 && convFight[i - 2] == 't');
            if (!isTwoLeftPriest)
            {
              // left alpha change
              if (i - 1 >= 0)
              {
                convFight[i - 1] = LeftToRightLetter(convFight[i - 1]);
              }
            }

            bool isTwoRightPriest = (i + 2 <= convFight.Length - 1 && convFight[i + 2] == 't');
            if (!isTwoRightPriest)
            {
              // right alpha change
              if (i + 1 <= convFight.Length - 1)
              {
                convFight[i + 1] = LeftToRightLetter(convFight[i + 1]);
              }
            }
          }
        }

        // left and right score diff
        for (int i = 0; i < convFight.Length; i++)
        {
          char ch = convFight[i];
          if (leftSideLetters.ContainsKey(ch))
          {
            leftScore += leftSideLetters[ch];
          }

          if (rightSideLetters.ContainsKey(ch))
          {
            rightScore += rightSideLetters[ch];
          }
        }

        if (leftScore > rightScore)
        {
          return "Left side wins!";
        }
        else if (leftScore < rightScore)
        {
          return "Right side wins!";
        }
        else
        {
          return "Let's fight again!";
        }
    }
 }
```

- 알파벳 전쟁 시리즈.
- 각 알파벳 마다 점수가 있다.
- t와 j는 상대 알파벳을 자기 알파벳 점수로 변경한다.
- 2칸 옆의 priest를 비교하는 부분이 중복되는 부분과 쓸데없이 길어지는 부분이 있다.

## Best Practices 1

```csharp
using System.Collections.Generic;
public class Kata
 {
        public enum Side
        {
            LeftSide,
            RightSide,
            NoSide
        }

        public static string AlphabetWar(string fight)
        {
            int pointDiff = 0;
            Dictionary<char, int> leftPowers = new Dictionary<char, int>(){
                {'w',4},
                {'p',3},
                {'b',2},
                {'s',1},
                {'t',0}
            };
            Dictionary<char, int> rightPowers = new Dictionary<char, int>(){
                {'m',4},
                {'q',3},
                {'d',2},
                {'z',1},
                {'j',0}
            };

            for (int i = 0; i < fight.Length; i++)
            {
                int value = 0;
                Side n1Wooloo = Side.NoSide;
                Side n2Wooloo = Side.NoSide;

                if (leftPowers.TryGetValue(fight[i], out value))
                {
                    if (value == 0) continue;

                    value *= -1;

                    findNeighborWooloos(i, leftPowers, rightPowers, fight, ref n1Wooloo, ref n2Wooloo);

                    if (n1Wooloo == Side.RightSide && n2Wooloo != Side.LeftSide || n2Wooloo == Side.RightSide && n1Wooloo != Side.LeftSide)
                        value *= -1;
                }
                else if (rightPowers.TryGetValue(fight[i], out value))
                {
                    if (value == 0) continue;

                    findNeighborWooloos(i, leftPowers, rightPowers, fight, ref n1Wooloo, ref n2Wooloo);

                    if (n1Wooloo == Side.LeftSide && n2Wooloo != Side.RightSide || n2Wooloo == Side.LeftSide && n1Wooloo != Side.RightSide)
                        value *= -1;
                }

                pointDiff += value;
            }

            if (pointDiff > 0) { return "Right side wins!"; }
            if (pointDiff < 0) { return "Left side wins!"; }
            return "Let's fight again!";
        }

        public static void findNeighborWooloos(int i, Dictionary<char, int> leftPowers, Dictionary<char, int> rightPowers, string fight, ref Side n1Wooloo, ref Side n2Wooloo)
        {
            int tempVal;
            if (i > 0)
            {
                if (leftPowers.TryGetValue(fight[i - 1], out tempVal))
                {
                    if (tempVal == 0) n1Wooloo = Side.LeftSide;
                }
                else if (rightPowers.TryGetValue(fight[i - 1], out tempVal))
                {
                    if (tempVal == 0) n1Wooloo = Side.RightSide;
                }
            }
            if (i < fight.Length - 1)
            {
                if (leftPowers.TryGetValue(fight[i + 1], out tempVal))
                {
                    if (tempVal == 0) n2Wooloo = Side.LeftSide;
                }
                else if (rightPowers.TryGetValue(fight[i + 1], out tempVal))
                {
                    if (tempVal == 0) n2Wooloo = Side.RightSide;
                }
            }
        }
 }
```

- 문자 자체를 변경 하는 작업을 하지 않는다.
- 문자를 비교하고 옆이 priest일 경우 점수만 변경 해준다.

## Best Practices 2

```csharp
 using System.Linq;
 using System.Text.RegularExpressions;
 public class Kata
 {
    public static string AlphabetWar(string fight)
    {
        string power = "wpbs_zdqm";
        fight = new Regex(@"(?<!t)[wpbs](?=j)|(?<=j)[wpbs](?!t)|(?<!j)[zdqm](?=t)|(?<=t)[zdqm](?!j)").Replace(fight, x => ""+ power[8 - power.IndexOf(x.Value)]);
        int result = fight.Aggregate(0, (a, b) => a + (power.IndexOf(b) == -1 ? 0 : power.IndexOf(b) - 4)); //
        return result == 0 ? "Let's fight again!" : $"{(result < 0 ? "Left":"Right")} side wins!";
    }
 }
```

- 정규표현식을 사용하여 간단하게 해결