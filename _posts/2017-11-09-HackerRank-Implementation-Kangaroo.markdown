---
layout: post
title: "[HackerRank #13] Implementation - Kangaroo"
excerpt: "HackerRank Implementation Kangaroo 문제 풀이"
date: 2017-11-09 21:32:00 +0900
tags: [hackerrank]
---

## 문제 요약

두 마리의 캥거루가 있다.

- 첫 번째 캥거루의 위치는 x1 이고, 캥거루는 v1 만큼 점프해서 이동한다.
- 두 번째 캥거루의 위치는 X2 이고, 캥커루는 V2 만큼 점프해서 이동한다.

그들이 같은 시간에 같은 장소에 착률 할 수 있으면 YES를 출력하세요.

인풋의 순서는 각각 x1, v1, x2, v2 이다.

Sample Input 0
```
0 3 4 2
```

Sample Output 0
```
YES
```

1. 0 -> 3 -> 6 -> 9 -> 12
2. 4 -> 6 -> 8 -> 10 -> 12

이 캥거리는 4번 점프하면 같은 장소에 만난다.

Sample Input 1
```
0 2 5 3
```

Sample Output 1
```
NO
```

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static string kangaroo(int x1, int v1, int x2, int v2) {        
        // first check impossible case : Position is in front and speed is faster
        if ((x1 > x2 && v1 > v2) || (x2 > x1 && v2 > v1))
        {
            return "NO";
        }
        
        // backPos and frontPos check
        int backPos = 0;
        int frontPos = 0;
        int backVel = 0;
        int frontVel = 0;
        if (x1 > x2)
        {
            backPos = x2;            
            frontPos = x1;
            backVel = v2;
            frontVel = v1;
        }
        else
        {            
            backPos = x1;
            frontPos = x2;            
            backVel = v1;
            frontVel = v2;
        }
        
        // loop jumping
        while (true)
        {
            backPos += backVel;
            frontPos += frontVel;
            
            if (backPos == frontPos)
                return "YES";
            else if (backPos > frontPos)
                return "NO";
        }
    }

    static void Main(String[] args) {
        string[] tokens_x1 = Console.ReadLine().Split(' ');
        int x1 = Convert.ToInt32(tokens_x1[0]);
        int v1 = Convert.ToInt32(tokens_x1[1]);
        int x2 = Convert.ToInt32(tokens_x1[2]);
        int v2 = Convert.ToInt32(tokens_x1[3]);
        string result = kangaroo(x1, v1, x2, v2);
        Console.WriteLine(result);
    }
}
```

## wanbo의 답안

python으로 문제를 풀었다.

다음 방정식에 해가 존재하는지만 파악 하면된다.

> x1 + t * v1 == x2 + t * v2

이것은 아래와도 같다.

> (x2-x1)%(v1-v2) == 0

```python
x1, v1, x2, v2 = map(int, raw_input().split())
X = [x1, v1]
Y = [x2, v2]
back = min(X, Y)
fwd = max(X, Y)
dist = fwd[0] - back[0]

while back[0] < fwd[0]:
    back[0] += back[1]
    fwd[0] += fwd[1]
    if fwd[0] - back[0] >= dist:
        break

print ["NO", "YES"][back[0] == fwd[0]]
}
```

## nasimoyz의 (x2-x1)%(v1-v2) == 0에 대한 설명


Input이 0 3 4 2 일때

```
k1 --- k2
x=0    x=4
v=3    v=2
``` 

k1이 도약할때 4에 오는지 확인해야 된다.

v1이 4의 요소인지 여부에 대한 간단한 문제입니다 (명확하게 넘어갈 것입니다).

```
(x2 - x1) % v1 = (4-0) % 3 = 1
```

k2가 움직이는 또 다른 경우를 상상해 봅시다 : v2 = 2

v1> v2 인 경우 v1이 결국 따라 잡을 것입니다. 

각 점프에서 K1이 3 단계를 진행하고 K2가 2 단계를 진행하면 K1은 각 점프에서 K2를 얻습니다 (각 점프는 점프보다 1 덜 떨어집니다).

이제 K1은 원래 거리 (4)에 가까워집니다.

```
4 6 8 10 12  <- K2
0 3 6  9 12  <- K1
4 3 2  1  0  <- Difference
1 1  1  1   <- Rate
```

거리가 닫히는 비율이 그들 사이의 원래 거리까지 합칠 수 있다면 (4), 결국 만나게 될 것입니다.

이제 만나지 않는 예를 들어 보겠습니다. 3 4 10 2

```
10 12 14 16 18  <- K2
 3  7 11 15 19  <- K1
 7  5  3  1 -1  <- Difference
 2  2  2  2    <- Rate
```

속도는 원래 거리의 요인이 아니므로 결코 만날 수 없습니다.. 7 % 2 = 1

## 느낀점

일단 푸는 방식 자체는 비슷한 것 같다.

C# 에서 제공해주는 Min과 Max 함수를 사용했으면 좀더 코드량은 줄어들었을것 같다.