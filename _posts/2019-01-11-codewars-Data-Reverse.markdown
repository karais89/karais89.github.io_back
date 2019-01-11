---
layout: post
title: "[Codewars #25] Data Reverse (6kyu)"
excerpt: "[Codewars #25] Data Reverse (6kyu) 문제 풀이"
date: 2019-01-11 23:43:00 +0900
tags: [codewars, kata]
---

## Instructions

Instructions
A stream of data is received and needs to be reversed.

Each segment is 8 bits long, meaning the order of these segments needs to be reversed, for example:

```
11111111 00000000 00001111 10101010
 (byte1) (byte2) (byte3) (byte4)
```

should become:
```
10101010 00001111 00000000 11111111
 (byte4) (byte3) (byte2) (byte1)
```

The total number of bits will always be a multiple of 8.

The data is given in an array as such:

```
[1,1,1,1,1,1,1,1,0,0,0,0,0,0,0,0,0,0,0,0,1,1,1,1,1,0,1,0,1,0,1,0]
```

## My Solution

```csharp
namespace Main{

using System;
public class Kata
{
  public static int[] DataReverse(int[] data)
  {
    const int SEG_COUNT = 8;
    int segNum = data.Length / SEG_COUNT;
    int[] dataRevers = new int[data.Length];
    for (int i = 0; i < data.Length; i++)
    {
      int currSeg = i / SEG_COUNT;
      int idx = ((segNum - currSeg - 1) * SEG_COUNT) + i % SEG_COUNT;
      dataRevers[idx] = data[i];
    }

    return dataRevers;
  }
}
}
```

8 비트씩 끊어서 뒤에다 정렬해 줘야되는 문제
for문에서 해당 인덱스값으로 변환해주어서 해결 하였다.

## Best Practices

```csharp
namespace Main
{

using System;
public class Kata
{
  public static int[] DataReverse(int[] data)
  {
     int[] bits = data;

     for(int i = 0; i < data.Length; i+=8)
     {
       Array.Reverse(bits, i, 8);
     }

     Array.Reverse(bits);

     return bits;
  }
}
}
```

8비트씩 끊어서 리버스를 해준다음에 전체 리버스를 해주면 해당되는 값이 나온다.

이걸 어떻게 생각했지?? 란 생각이 드네.. 기발하네..

```
(원래 값)
11111111 00000000 00001111 10101010
(8비트 리버스)
11111111 00000000 11110000 01010101
(전체 리버스)
10101010 00001111 00000000 11111111
```