---
layout: post
title: "[HackerRank #20] Implementation - Bon Appétit"
excerpt_separator: "HackerRank Implementation Bon Appétit 문제 풀이"
date: 2017-11-17 09:36:00 +0900
tags: [hackerrank]
---

## 문제 요약

안나와 브라이언이 식당에서 주문한 물건들 n 그러나 안나는 알레르기로 인해 먹는 것을 거부합니다. k개의 아이템을
 
수표가 오면, 그들은 그들이 공유하는 모든 항목의 비용을 나누기로 결정합니다. 

그러나 브라이언은 항목을 분리하지 않고 실수로 안나에게 청구했음을 잊어 버렸을 수 있습니다.

당신은 각 아이템의 비용과 브라이언이 계산서의 일부분에 대한 안나에게 청구 한 총 금액을 받습니다.

청구서가 상당히 분할되면 Bon Appetit을 인쇄하십시오. 그렇지 않으면 브라이언이 안나에게 환불해야하는 금액을 인쇄하십시오.

첫번째 인자는 아이템의 개수이고 두번째 인자는 안나가 먹지 못하는 음식의 인덱스 값 이다\.

두번째 라인의 첫번째 줄에서 첫번째 인자로 받은 인덱스는 각 음식의 값들이고.

세번째 라인은 브라이언이 안나에게 청구한 금액이다.

Sample Input 0
```
4 1
3 10 2 9
12
```

Sample Output 0
```
5
```

안나는 c[1] = 10을 먹지 못한다.

총 비용은 3+2+9=14이다. 그녀의 비용을 사람 수로 나누면 7이다.

브라이언은 그녀에게 12달러를 청구하였고, 그녀는 7달러면 내면 된다.

그러므로 우리는 안나에게 과다 청구된 금액을 출력하면 된다 12-7=5이다. 

Sample Input 1
```
4 1
3 10 2 9
7
```

Sample Output 1
```
Bon Appetit
```

정확하게 안나가 청구될 금액과 일치 하므로 Bon Appetit을 출력한다.

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int bonAppetit(int n, int k, int b, int[] ar) {
        // Complete this function
        int sum = 0;
        for (int i = 0; i < n; i++)
        {
            if (k == i)
            {
                continue;
            }
            
            sum += ar[i];
        }
        int charge = sum / 2;        
        return b - charge;
    }

    static void Main(String[] args) {
        string[] tokens_n = Console.ReadLine().Split(' ');
        int n = Convert.ToInt32(tokens_n[0]);
        int k = Convert.ToInt32(tokens_n[1]);
        string[] ar_temp = Console.ReadLine().Split(' ');
        int[] ar = Array.ConvertAll(ar_temp,Int32.Parse);
        int b = Convert.ToInt32(Console.ReadLine());
        int result = bonAppetit(n, k, b, ar);
        if (result == 0)
        {
            Console.WriteLine("Bon Appetit");
        }
        else
        {
            Console.WriteLine(result);        
        }
    }
}
```

## AllisonP의 답안

linq를 사용하여 풀었다.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

class Solution {
    static void Main(String[] args) {
        int K = Console.ReadLine().Split(' ').Select(Int32.Parse).ToArray()[1];
        
        List<int> ItemList = Console.ReadLine().Split(' ').Select(Int32.Parse).ToList();
        long CorrectTotal = (ItemList.Sum(m => m) - ItemList[K]) / 2;
        long AmountCharged = Convert.ToUInt32(Console.ReadLine());
        
        if (CorrectTotal == AmountCharged)
            Console.WriteLine("Bon Appetit");
        else {
            Console.WriteLine(AmountCharged - CorrectTotal);
        }
    }
}
```

## 느낀점

알고리즘 문제들을 풀다보면 c#의 경우 linq를 사용하면 쉽게 풀 수 있는 것들이 많다.

사실 linq의 사용을 의도적으로 피한 경향이 있었다.[^1]

하지만 linq관련 문제는 여러가지 방법으로 우회 방법이 존재하며, 이제는 슬슬 적용을 할 단계인것 같습니다.

[^1]: 유니티의 경우 mono 2.x 버전의 버그로 정상 작동 불가.