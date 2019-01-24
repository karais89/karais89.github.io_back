---
layout: post
title: "[Codewars #49] Take a Ten Minute Walk (6kyu)"
excerpt: "[Codewars #49] Take a Ten Minute Walk (6kyu) 문제 풀이"
date: 2019-01-24 23:36:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/54da539698b8a2ad76000228/train/csharp)

You live in the city of Cartesia where all roads are laid out in a perfect grid. You arrived ten minutes too early to an appointment, so you decided to take the opportunity to go for a short walk. The city provides its citizens with a Walk Generating App on their phones -- everytime you press the button it sends you an array of one-letter strings representing directions to walk (eg. ['n', 's', 'w', 'e']). You always walk only a single block in a direction and you know it takes you one minute to traverse one city block, so create a function that will return true if the walk the app gives you will take you exactly ten minutes (you don't want to be early or late!) and will, of course, return you to your starting point. Return false otherwise.


> Note: you will always receive a valid array containing a random assortment of direction letters ('n', 's', 'e', or 'w' only). It will never give you an empty array (that's not a walk, that's standing still!).


## My Solution

```csharp
public class Kata
{
  public static bool IsValidWalk(string[] walk)
  {
    //insert brilliant code here
    if (walk.Length != 10)
    {
      return false;
    }

    int dirX = 0, dirY = 0;
    for (int i = 0; i < walk.Length; i++)
    {
      switch (walk[i])
      {
        case "n":
          dirY++;
          break;
        case "s":
          dirY--;
          break;
        case "e":
          dirX++;
          break;
        case "w":
          dirX--;
          break;
        default:
          // nothing
          break;
      }
    }

    return dirX == 0 && dirY == 0;
  }
}
```

- 배열의 길이가 10이고, 모두 이동한 후에 제자리 걸음이면 true 아니면 false 이다.

## Best Practices

```csharp
public class Kata
{
  public static bool IsValidWalk(string[] walk)
  {
    if (walk.Length != 10) return false;
    var x = 0; var y = 0;
    foreach (var dir in walk)
    {
        if (dir == "n") x++;
        else if (dir == "s") x--;
        else if (dir == "e") y++;
        else if (dir == "w") y--;
    }
    return x == 0 && y == 0;
  }
}
```

- for와 foreach 차이 if 와 switch 차이 정도만 다르고 논리는 똑같다.