---
layout: post
title: "[HackerRank #10] Warmup - Time Conversion"
excerpt: "HackerRank Warmup Time Conversion 문제 풀이"
date: 2017-11-07 16:21:00 +0900
tags: [hackerrank]
---

## 문제 요약

일반 시간을 군대 시간으로 변경하기.

Sample Input
```
07:05:45PM
```

Sample Output
```
19:05:45
```

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static string timeConversion(string s) {
        // Complete this function
        string time = s.Substring(0, 8);
        string timeFormat = s.Substring(8);        
        
        string[] timeSplit = time.Split(':');
        int hour = 0;
        if (timeFormat == "PM")
        {
            hour = Convert.ToInt32(timeSplit[0]) + 12;
            if (hour == 24)
            {
                hour = 12;
            }
        }
        else            
        {
            hour = Convert.ToInt32(timeSplit[0]);
            if (hour == 12)
            {
                hour = 0;
            }
        }
        return string.Format("{0:00}:{1:00}:{2:00}", hour, timeSplit[1], timeSplit[2]);    
    }

    static void Main(String[] args) {
        string s = Console.ReadLine();
        string result = timeConversion(s);
        Console.WriteLine(result);
    }
}
```

## vatsalchanana의 답안

```cpp
#include<iostream>
#include<cstdio>

using namespace std;

int main() {
    string s;
    cin >> s;

    int n = s.length();
    int hh, mm, ss;
    hh = (s[0] - '0') * 10 + (s[1] - '0');
    mm = (s[3] - '0') * 10 + (s[4] - '0');
    ss = (s[6] - '0') * 10 + (s[7] - '0');

    if (hh < 12 && s[8] == 'P') hh += 12;
    if (hh == 12 && s[8] == 'A') hh = 0;

    printf("%02d:%02d:%02d\n", hh, mm, ss);

    return 0;
}
```

## Jashin의 답안

```java
public static void main(String[] args) {
        
		Scanner scan = new Scanner(System.in);
        String time = scan.next();
        String tArr[] = time.split(":");
        String AmPm = tArr[2].substring(2,4);
        int hh,mm,ss;
        hh = Integer.parseInt(tArr[0]);
        mm = Integer.parseInt(tArr[1]);
        ss = Integer.parseInt(tArr[2].substring(0,2));
        
        String checkPM = "PM",checkAM ="AM";
        int h = hh;
        if(AmPm.equals(checkAM) && hh==12)
        	h=0;
        else if(AmPm.equals(checkPM)&& hh<12)
        	h+=12;
        
        System.out.printf("%02d:%02d:%02d",h,mm,ss);
    }
```

## 느낀점

AM, PM을 군대 시간으로 바꾸는 문제이다.