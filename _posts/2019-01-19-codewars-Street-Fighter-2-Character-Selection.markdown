---
layout: post
title: "[Codewars #35] Street Fighter 2 - Character Selection (6kyu)"
excerpt: "[Codewars #35] Street Fighter 2 - Character Selection (6kyu) 문제 풀이"
date: 2019-01-19 00:12:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/5853213063adbd1b9b0000be/train/csharp)

Short Intro

Some of you might remember spending afternoons playing Street Fighter 2 in some Arcade back in the 90s or emulating it nowadays with the numerous emulators for retro consoles.

Can you solve this kata? Suuure-You-Can!

UPDATE: a new kata's harder version is available here.

The Kata

You'll have to simulate the video game's character selection screen behaviour, more specifically the selection grid. Such screen looks like this:

Selection Grid Layout in text:

```
| Ryu | E.Honda | Blanka | Guile | Balrog | Vega |
| Ken | Chun Li | Zangief | Dhalsim | Sagat | M.Bison |
```

Input

- the list of game characters in a 2x6 grid;
- the initial position of the selection cursor (top-left is (0,0));
- a list of moves of the selection cursor (which are up, down, left, right);

Output

- the list of characters who have been hovered by the selection cursor after all the moves (ordered and with repetition, all the ones after a move, wether successful or not, see tests);

Rules

Selection cursor is circular horizontally but not vertically!

As you might remember from the game, the selection cursor rotates horizontally but not vertically; that means that if I'm in the leftmost and I try to go left again I'll get to the rightmost (examples: from Ryu to Vega, from Ken to M.Bison) and vice versa from rightmost to leftmost.

Instead, if I try to go further up from the upmost or further down from the downmost, I'll just stay where I am located (examples: you can't go lower than lowest row: Ken, Chun Li, Zangief, Dhalsim, Sagat and M.Bison in the above image; you can't go upper than highest row: Ryu, E.Honda, Blanka, Guile, Balrog and Vega in the above image).

Test

For this easy version the fighters grid layout and the initial position will always be the same in all tests, only the list of moves change.

Notice : changing some of the input data might not help you.

Examples

1.
```
fighters = [
    ["Ryu", "E.Honda", "Blanka", "Guile", "Balrog", "Vega"],
    ["Ken", "Chun Li", "Zangief", "Dhalsim", "Sagat", "M.Bison"]
]
initial_position = (0,0)
moves = ['up', 'left', 'right', 'left', 'left']
```

then I should get:

```
['Ryu', 'Vega', 'Ryu', 'Vega', 'Balrog']
```

as the characters I've been hovering with the selection cursor during my moves. Notice: Ryu is the first just because it "fails" the first up See test cases for more examples.

2.

```
fighters = [
    ["Ryu", "E.Honda", "Blanka", "Guile", "Balrog", "Vega"],
    ["Ken", "Chun Li", "Zangief", "Dhalsim", "Sagat", "M.Bison"]
]
initial_position = (0,0)
moves = ['right', 'down', 'left', 'left', 'left', 'left', 'right']
```

Result:
```
['E.Honda', 'Chun Li', 'Ken', 'M.Bison', 'Sagat', 'Dhalsim', 'Sagat']
```

OFF-TOPIC

Some music to get in the mood for this kata :

Street Fighter 2 - selection theme

## My Solution

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Kata
{
    public string[] StreetFighterSelection(string[][] fighters, int[] position, string[] moves)
    {
        int col = fighters.Length;
        int row = fighters[0].Length;

        int curCol = position[0];
        int curRow = position[1];

        List<string> charTracking = new List<string>();
        for (int i = 0; i < moves.Length; i++)
        {
          int moveCol;
          int moveRow;
          switch (moves[i])
          {
            case "up":
              moveCol = curCol - 1;
              if (moveCol >= 0)
              {
                curCol = moveCol;
              }
              break;
            case "down":
              moveCol = curCol + 1;
              if (moveCol < col)
              {
                curCol = moveCol;
              }
              break;
            case "left":
              moveRow = curRow - 1;
              curRow = moveRow < 0 ? row - 1 : moveRow;
              break;
            case "right":
              moveRow = curRow + 1;
              curRow = moveRow > row - 1 ? 0 : moveRow;
              break;
          }
          charTracking.Add(fighters[curCol][curRow]);
        }
        return charTracking.ToArray();
    }
}
```

- 게임 캐릭터의 리스트는 2x6 그리드 이다.
- 선택 커서의 초기 위치는 0,0 이다.
- 선택 커서는 위, 아래, 좌, 우로 이동할 수 있다.
- 커서의 순환은 (양 옆 혹은 위) 수평으로만 가능 하다.


## Best Practices

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Kata
{
  public string[] StreetFighterSelection(string[][] fighters, int[] position, string[] moves)
  {
    var results = new List<string>();
    var row = 0;
    var col = 0;

    foreach (var s in moves) {
      switch (s) {
        case "up": row -= 1;
          break;
        case "down": row += 1;
          break;
        case "left": col -= 1;
          break;
        case "right": col += 1;
          break;
      }
      if (row < 0) row = 0;
      if (row == fighters.Length) row--;
      if (col == fighters[0].Length) col = 0;
      if (col == -1) col = fighters[0].Length - 1;

      results.Add(fighters[row][col]);
    }

    return results.ToArray();
  }
}
```

거의 유사하지만, 범위 체크를 맨 아래에서 해주었다.
범위체크를 움직일때마다 해주는 것보다 맨 마지막에 해주는게 깔끔한 느낌도 든다.