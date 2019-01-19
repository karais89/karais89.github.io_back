---
layout: post
title: "[Codewars #37] Maze Runner (6kyu)"
excerpt: "[Codewars #37] Maze Runner (6kyu) 문제 풀이"
date: 2019-01-19 20:20:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/58663693b359c4a6560001d6/train/csharp)

Introduction

```
  Welcome Adventurer. Your aim is to navigate the maze and reach the finish point without touching any walls. Doing so will kill you instantly!
```

Maze Runner
Task
```
  You will be given a 2D array of the maze and an array of directions. Your task is to follow the directions given. If you reach the end point before all your moves have gone, you should return Finish. If you hit any walls or go outside the maze border, you should return Dead. If you find yourself still in the maze after using all the moves, you should return Lost.
```

The Maze array will look like
```
maze = [[1,1,1,1,1,1,1],
        [1,0,0,0,0,0,3],
        [1,0,1,0,1,0,1],
        [0,0,1,0,0,0,1],
        [1,0,1,0,1,0,1],
        [1,0,0,0,0,0,1],
        [1,2,1,0,1,0,1]]
```

..with the following key

```
0 = Safe place to walk
1 = Wall
2 = Start Point
3 = Finish Point
```

```
  direction = ["N","N","N","N","N","E","E","E","E","E"] == "Finish"
```

Rules
```
  1. The Maze array will always be square i.e. N x N but its size and content will alter from test to test.
2. The start and finish positions will change for the final tests.
3. The directions array will always be in upper case and will be in the format of N = North, E = East, W = West and S = South.
```

Good luck, and stay safe!

Kata Series

If you enjoyed this, then please try one of my other Katas. Any feedback, translations and grading of beta Katas are greatly appreciated. Thank you.

## My Solution

```csharp
using System;

namespace CodeWars
{
    class Kata
    {
        private enum MazeState
        {
          SafePlaceToWalk,
          Wall,
          StartPoint,
          EndPoint
        };

        public struct Point
        {
          public int x;
          public int y;

          public override string ToString()
          {
            return $"[{x}, {y}]";
          }
        }

        public Point FindStartPoint(int[,] maze)
        {
          for (int i = 0; i < maze.GetLength(0); i++)
          {
            for (int j = 0; j < maze.GetLength(1); j++)
            {
              if (maze[i,j] == (int)MazeState.StartPoint)
              {
                Point p = new Point();
                p.y = i;
                p.x = j;
                return p;
              }
            }
          }

          Point notFoundPoint = new Point();
          notFoundPoint.x = -1;
          notFoundPoint.y = -1;
          return notFoundPoint;
        }

        public string mazeRunner(int[,] maze, string[] directions)
        {
          // Code here
          Point startPoint = FindStartPoint(maze);
          Point nextPoint = startPoint;

          for (int i = 0; i < directions.Length; i++)
          {
            switch(directions[i])
            {
              case "N": // up
                nextPoint.y--;
                break;
              case "S": // down
                nextPoint.y++;
                break;
              case "E": // right
                nextPoint.x++;
                break;
              case "W": // left
                nextPoint.x--;
                break;
            }

            // out side check
            if (nextPoint.x < 0 || nextPoint.x >= maze.GetLength(0) ||
                nextPoint.y < 0 || nextPoint.y >= maze.GetLength(1))
            {
              return "Dead";
            }

            switch ((MazeState)maze[nextPoint.y, nextPoint.x])
            {
              case MazeState.Wall:
                return "Dead";
              case MazeState.EndPoint:
                return "Finish";
              default:
                // nothing
                break;
            }
          }

          return "Lost";
        }
    }
}
```

- direction 배열에 있는 스트링 값으로 start point 부터 시작하여 진행.
- 중간에 벽에 닿으면 Dead 리턴
- 모든 행동이 끝나기전에 finish에 도착한다면 Finish 리턴
- 모든 행동이 끝나도 죽지 않았거나, finish 지점에 도착 하지 못했다면 Lost 리턴


## Best Practices

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace CodeWars
{
    class Kata
    {
        public string mazeRunner(int[,] maze, string[] directions)
        {
            int startX = 0;
            int startY = 0;
            double len = Math.Sqrt(maze.Length);
            for (int x = 0; x < len; x++)
            {
                for (int y = 0; y < len; y++)
                {
                    if (maze[y, x] == 2) { startX = x; startY = y; }
                }
            }
            for (int x = 0; x < directions.Length; x++)
            {
                switch (directions[x])
                {
                    case "N": startY -= 1; break;
                    case "E": startX += 1; break;
                    case "S": startY += 1; break;
                    case "W": startX -= 1; break;
                }
                if (startY < 0 || startY > len - 1 || startX < 0 || startX > len - 1 || maze[startY, startX] == 1) { return "Dead"; }
                if (maze[startY,startX] == 3) { return "Finish"; }
            }

            return "Lost";
        }
    }
}
```

- 논리는 거의 비슷해 보인다.
- 예외 처리 및 매직 넘버를 사용하는 방식이라 코드가 더 짧다.
- 이게 더 베스트한 방식인지는 의문이 든다.