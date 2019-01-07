---
layout: post
title: "[Codewars #17] Speed Control (7kyu)"
excerpt: "[Codewars #17] Speed Control (7kyu) 문제 풀이"
date: 2019-01-07 14:17:00 +0900
tags: [codewars, kata]
---

## Instructions

In John's car the GPS records every s seconds the distance travelled from an origin (distances are measured in an arbitrary but consistent unit). For example, below is part of a record with s = 15:

```
x = [0.0, 0.19, 0.5, 0.75, 1.0, 1.25, 1.5, 1.75, 2.0, 2.25]
```

The sections are:
```
0.0-0.19, 0.19-0.5, 0.5-0.75, 0.75-1.0, 1.0-1.25, 1.25-1.50, 1.5-1.75, 1.75-2.0, 2.0-2.25
```

We can calculate John's average hourly speed on every section and we get:
```
[45.6, 74.4, 60.0, 60.0, 60.0, 60.0, 60.0, 60.0, 60.0]
```

Given s and x the task is to return as an integer the *floor* of the maximum average speed per hour obtained on the sections of x. If x length is less than or equal to 1 return 0 since the car didn't move.

Example:
with the above data your function gps(s, x)should return 74

Note
With floats it can happen that results depends on the operations order. To calculate hourly speed you can use:

(3600 * delta_distance) / s.

## My Solution

```csharp
using System;

public class GpsSpeed {

    public static int Gps(int s, double[] x) {
        if (x == null || x.Length <= 1)
          return 0;

        // your code
        double max = double.MinValue;
        for (int i = 0; i < x.Length; i++)
        {
          if (i+1 < x.Length)
          {
            double distance = x[i+1] - x[i];
            double speed = ((3600*distance) / s);
            if (max < speed)
            {
              max = speed;
            }
          }
        }
        return (int)(max);
    }
}
```

처음에 문제를 잘못 이해해서 다른 방법으로 풀었다.

## Best Practices

```csharp
using System;
using System.Linq;

public class GpsSpeed
{
    public static int Gps(int s, double[] x)
    {
      if(x.Length > 2)
      {
        var averageSpeeds = x.Skip(1).Select((distance, index) => ((distance - x[index]) / s) * 3600);
        return Convert.ToInt32(Math.Floor(averageSpeeds.Max()));
      }

      return 0;
    }
}
```

Linq 사용

> Skip : 무시할 개수