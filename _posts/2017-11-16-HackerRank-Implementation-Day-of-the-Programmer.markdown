---
layout: post
title: "[HackerRank #19] Implementation - Day of the Programmer"
excerpt: "HackerRank Implementation Day of the Programmer 문제 풀이"
date: 2017-11-16 21:06:00 +0900
tags: [hackerrank]
---

## 문제 요약

마린은 타임머신을 발명했으며 시간 여행을 통해 1700~2700년 사이의 프로그래머의날[^1] (일년중 256th)에 방문하려고 한다.

1700년에서 1917년에는 러시아에서의 공식 달력은 줄리안 달력이고, 1919년에는 그레고릭 달력 시스템이었다.

1918년에 줄리안 달력에서 그레고릭 달력으로 변경이 일어났고, 1월 31일 이후 다음날 2월 14일 변경이 됐다.

이 말은 러시아에서 1918년에는 2월 14일은 1월 32일 이었다. (중간에 14일은 간의 기간은 달력 변경으로 삭제)

두 가지 달력 시스템에서 2 월은 가변 일수가있는 유일한 달입니다.

윤년 기간에는 29 일, 그 외 모든 연도에는 28 일이 있습니다

줄리아 달력에서 윤년은 4로 나눌 수 있습니다. 그레고리력에서 윤년은 다음 중 하나입니다.

- 400으로 나눌수 있다.
- 4로 나누어지지만 100으로는 나누어지지 않는다.

1년을 감안할 때 그 해의 공식 러시아 달력에 따라 해당 연도의 날짜를 찾으십시오.

그런 다음 dd.mm.yyyy 형식으로 인쇄하십시오. 여기서 dd는 두 자리 날이고 mm은 두 자리 월이며 yyyy는 y입니다.

Sample Input 0
```
2017
```

Sample Output 0
```
13.09.2017
```

2017년은 1월은 31일, 2월은 28일, 3월은 31일, 4월은 30일 ,5월은 31일, 6월은 30일, 7월은 31일,
8월은 31일이다.
31+28+31+30+31+30+31+30+31+31 = 243이다.
프로그래머의 날은 256번째 날이므로. 256-243=13이다. 그러므로 9월 13일이 프로그래머의 날이다.

Sample Input 0
```
2016
```

Sample Output 0
```
12.09.2017
```

위와 같은 방식이지만 2016년은 2월이 29일인 윤년이다. 그러므로 
31+29+31+30+31+30+31+30+31+31 = 244이고
256-244=12이다. 그러므로 9월 12일이 프로그래머의 날이다.


[^1]: 0x100일째의 날을 기념한다고 하는 날. 10진 수로 변경하면 매 해의 256번째 날.


## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int[] GetDays(int year) {
        int[] days = new int[12] {
            31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31
        };      
        
        bool isJulianCalendar = year <= 1917;
        bool isGregorianCalendar = year >= 1919;
        
        if (isJulianCalendar)
        {
            if (year % 4 == 0)
            {
                days[1] = 29;
            }
        }
        else if (isGregorianCalendar)
        {
            if ((year % 4 == 0 && year % 100 != 0) ||
                (year % 400 == 0))
            {
                days[1] = 29;
            }
        }
        else            
        {
            // julianCalendar & isGregorianCalendar
            // January 31 after -> February 14
            days[1] = 28 - 14 + 1;
        }
        
        return days;
    }
    
    static string solve(int year){
        // Complete this function    
        int[] days = GetDays(year);
        int programmerDay = 256;
        int y = year;
        int m = 0;
        int d = 0;
        for (int i = 0; i < days.Length; i++)
        {
            programmerDay -= days[i];
            if (programmerDay < 0)
            {
                m = i + 1;
                d = programmerDay + days[i];
                break;
            }
        }
        
        return String.Format("{0:00}.{1:00}.{2:0000}", d, m, y);
    }

    static void Main(String[] args) {
        int year = Convert.ToInt32(Console.ReadLine());
        string result = solve(year);
        Console.WriteLine(result);
    }
}
```

## _mfv_의 답안

```cpp
#pragma GCC diagnostic ignored "-Wunused-result"

#include <cstdio>
#include <cassert>

int main() {
    int y;
    scanf("%d", &y);
    int daysInFebruary;
    if (y == 1918) {
        daysInFebruary = 28 - 13;
    } else if (y < 1918) {
        if (y % 4 == 0) {
            daysInFebruary = 29;
        } else {
            daysInFebruary = 28;
        }
    } else {
        if ((y % 4 == 0 && y % 100 != 0) || y % 400 == 0) {
            daysInFebruary = 29;
        } else {
            daysInFebruary = 28;
        }
    }
    int day = 256 - 31 - daysInFebruary - 31 - 30 - 31 - 30 - 31 - 31;
    assert(1 <= day && day <= 30);
    printf("%02d.%02d.%04d", day, 9, y);
    return 0;
}
```

## 느낀점

문제 자체가 더 어려운 느낌.

문제를 풀려면 그레고릭 달력 줄리안 달력 개념과 윤년 계산 법 등을 알고 있어야 한다.

문제를 풀면서 프로그래머의 날이 있다는 사실 도 알았다..

문제 자체는 출제자와 거의 똑같은 방식으로 푼 것 같다.