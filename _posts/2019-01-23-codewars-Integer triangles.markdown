---
layout: post
title: "[Codewars #43] Integer triangles (5kyu)"
excerpt: "[Codewars #43] Integer triangles (5kyu) 문제 풀이"
date: 2019-01-23 09:31:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/55db7b239a11ac71d600009d/train/csharp)

You have to give the number of different integer triangles with one angle of 120 degrees which perimeters are under or equal a certain value. Each side of an integer triangle is an integer value.
```
give_triang(max. perimeter) --------> number of integer triangles,
```
with sides a, b, and c integers such that:

a + b + c <= max. perimeter

See some of the following cases
```
give_triang(5) -----> 0 # No Integer triangles with perimeter under or equal five
give_triang(15) ----> 1 # One integer triangle of (120 degrees). It's (3, 5, 7)
give_triang(30) ----> 3 # Three triangles: (3, 5, 7), (6, 10, 14) and (7, 8, 13)
give_triang(50) ----> 5 # (3, 5, 7), (5, 16, 19), (6, 10, 14), (7, 8, 13) and (9, 15, 21) are the triangles with perim under or equal 50.
```

- 서로 다른 길이의 삼각형을 지정해야 된다.
- 삼각형 중 하나는 120도 각도 이고, 둘레가 max 값 보다 작은 정수 삼각형을 구해야 한다.

## My Solution

> 문제를 풀지 못했다. 정확한 문제 이해가 되지 않은 상태 였다.

- 검색어 : integer triangle 120 degree
- 한국 웹 사이트 검색 결과를 찾지 못했다.
- https://en.wikipedia.org/wiki/Integer_triangle
- http://www.geocities.ws/fredlb37/node11.html
- a² + ab + b² = c²

## Best Practices

```csharp
using System;
public class IntTriangles
{
        // Integer triangles with 120° angle are such that a² + ab + b² = c²
        // http://www.had2know.com/academics/integer-triangles-120-degree-angle.html
        public static int GiveTriang(int per)
        {
            var count = 0;
            for(var a = 1; a < per; a++)
            {
                for(var b = a+1; a+b < per; b++)
                {
                    var left = a * a + a * b + b * b;
                    var c = (int)Math.Sqrt(left);
                    if (left == c*c && c > b && a+b+c <= per)
                    {
                        count++;
                    }
                }
            }
            return count;
        }

}
```