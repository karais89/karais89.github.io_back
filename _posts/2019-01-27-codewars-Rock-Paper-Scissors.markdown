---
layout: post
title: "[Codewars #57] Rock Paper Scissors! (8kyu)"
excerpt: "[Codewars #57] Rock Paper Scissors! (8kyu) 문제 풀이"
date: 2019-01-27 22:54:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/rock-paper-scissors/train/csharp)

Rock Paper Scissors
Let's play! You have to return which player won! In case of a draw return Draw!.

Examples:
```
rps('scissors','paper') // Player 1 won!
rps('scissors','rock') // Player 2 won!
rps('paper','paper') // Draw!
```

## My Solution

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text.RegularExpressions;

public class Kata
{
  public int getState(string str)
  {
    switch (str)
    {
      case "rock": return 1;
      case "scissors": return 2;
      case "paper": return 3;
    }
    return 0;
  }

  public string Rps(string p1, string p2)
  {
    // "rock", "scissors", "paper"
    int diff = getState(p1) - getState(p2);
    if (diff == -1 || diff == 2)
    {
      return "Player 1 won!";
    }
    else if (diff == 0)
    {
      return "Draw!";
    }

    return "Player 2 won!";
  }
}
```

- 가독성이 떨어진다.
- 베스트 솔루션 방식처럼 케이스 바이 케이스 별로 스트링을 구해주는게 더 나은 방법 인듯..

## Best Practices

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text.RegularExpressions;

public class Kata
{
  public string Rps(string p1, string p2)
  {
    if (p1 == p2)
      return "Draw!";

    if (((p1 == "rock") && (p2 == "scissors")) ||
        ((p1 == "scissors") && (p2 == "paper")) ||
        ((p1 == "paper") && (p2 == "rock")))
    {
      return "Player 1 won!";
    }
    else
    {
      return "Player 2 won!";
    }
  }
}
```