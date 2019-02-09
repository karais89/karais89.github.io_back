---
layout: post
title: "[Codewars #64] Growth of a Population (7kyu)"
excerpt: "[Codewars #64] Growth of a Population (7kyu) 문제 풀이"
date: 2019-01-31 11:33:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/563b662a59afc2b5120000c6/train/csharp)

In a small town the population is p0 = 1000 at the beginning of a year. The population regularly increases by 2 percent per year and moreover 50 new inhabitants per year come to live in the town. How many years does the town need to see its population greater or equal to p = 1200 inhabitants?

```
At the end of the first year there will be:
1000 + 1000 * 0.02 + 50 => 1070 inhabitants

At the end of the 2nd year there will be:
1070 + 1070 * 0.02 + 50 => 1141 inhabitants (number of inhabitants is an integer)

At the end of the 3rd year there will be:
1141 + 1141 * 0.02 + 50 => 1213

It will need 3 entire years.
```

More generally given parameters:

p0, percent, aug (inhabitants coming or leaving each year), p (population to surpass)

the function nb_year should return n number of entire years needed to get a population greater or equal to p.

aug is an integer, percent a positive or null number, p0 and p are positive integers (> 0)

```
Examples:
nb_year(1500, 5, 100, 5000) -> 15
nb_year(1500000, 2.5, 10000, 2000000) -> 10
```

Note: Don't forget to convert the percent parameter as a percentage in the body of your function: if the parameter percent is 2 you have to convert it to 0.02.

## My Solution

```csharp
using System;

class Arge {

    public static int NbYear(int p0, double percent, int aug, int p) {
        // your code
        int population = p0;
        double per = percent / 100;
        int year = 0;

        while (true)
        {
          population += (int)(population * per) + aug;
          year++;

          if (population >= p)
          {
            return year;
          }
        }
        return year;
    }
}
```

## Best Practices 1

```csharp
using System;

class Arge {

    public static int NbYear(int p0, double percent, int aug, int p) {
        int year;
        for (year = 0; p0 < p; year++)
          p0 += (int)(p0*percent/100) + aug;
        return year;
    }
}
```

- for문을 사용하여 해결

## Best Practices 2

```csharp
using System;

class Arge
{
    public static int NbYear(int p0, double percent, int aug, int p)
    {
        int expectedPopulation = p;

        int cityPopulation = p0;
        int newInhabitantsPerYear = aug;
        double increasePerYear = percent / 100;

        int years = 0;

        while(cityPopulation < expectedPopulation)
        {
            cityPopulation += (int)(cityPopulation * increasePerYear) + newInhabitantsPerYear;

            years++;
        }

        return years;
    }
}
```

- 변수 네이밍이 괜찮아보여서, 기록해봄.