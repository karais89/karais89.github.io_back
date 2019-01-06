---
layout: post
title: "[Codewars #16] Vasya - Clerk (6kyu)"
excerpt: "[Codewars #16] Vasya - Clerk (6kyu) 문제 풀이"
date: 2019-01-06 18:27:00 +0900
tags: [codewars, kata]
---

## Instructions

The new "Avengers" movie has just been released! There are a lot of people at the cinema box office standing in a huge line. Each of them has a single 100, 50 or 25 dollars bill. An "Avengers" ticket costs 25 dollars.

Vasya is currently working as a clerk. He wants to sell a ticket to every single person in this line.

Can Vasya sell a ticket to each person and give the change if he initially has no money and sells the tickets strictly in the order people follow in the line?

Return YES, if Vasya can sell a ticket to each person and give the change with the bills he has at hand at that moment. Otherwise return NO.

```
Line.Tickets(new int[] {25, 25, 50}) // => YES
Line.Tickets(new int[] {25, 100}) // => NO. Vasya will not have enough money to give change to 100 dollars
Line.Tickets(new int[] {25, 25, 50, 50, 100}) // => NO. Vasya will not have the right bills to give 75 dollars of change (you can't make two bills of 25 from one of 50)
```

## My Solution

```csharp
using System;

public class Line
    {
        public static string Tickets(int[] peopleInLine)
        {
          int[] bills = new int[3];
          for (int i = 0; i < peopleInLine.Length; i++)
          {
            int bill = peopleInLine[i];
            if (bill == 25)
            {
              bills[0]++;
              continue;
            }

            if (bill == 50)
            {
              bills[1]++;
              if (bills[0] <= 0)
              {
                return "NO";
              }
              bills[0]--;
            }
            else if (peopleInLine[i] == 100)
            {
              bills[2]++;
              if (bills[0] <= 0 || bills[1] <= 0)
              {
                return "NO";
              }
              bills[0]--;
              bills[1]--;
            }
          }
          return "YES";
        }
    }
```

문제 자체가 재미있었다.

문제 해결은 했는데 오류가 있다. 테스트 케이스에서 통과를 한 경우 인듯..
100 달러를 냈을때 25달러 3개가 있는 경우에 대한 if문이 없다.
결국 이건 잘못된 코드.

## Best Practices

```csharp
using System;

public class Line
{
    public static string Tickets(int[] peopleInLine)
    {
        int twentyFives = 0, fifties = 0;

        foreach (var bill in peopleInLine)
        {
            switch (bill)
            {
                case 25:
                    ++twentyFives;
                    break;
                case 50:
                    --twentyFives;
                    ++fifties;
                    break;
                case 100:
                    if (fifties == 0)
                    {
                        twentyFives -= 3;
                    }
                    else
                    {
                        --twentyFives;
                        --fifties;
                    }
                    break;
            }

            if (twentyFives < 0 || fifties < 0)
            {
                return "NO";
            }
        }

        return "YES";
    }
}

```

확실히 이 방법이 가장 깔끔한 방법인듯 하다.