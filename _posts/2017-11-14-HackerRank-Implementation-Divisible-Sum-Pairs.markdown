---
layout: post
title: "[HackerRank #17] Implementation - Divisible Sum Pairs"
excerpt: "HackerRank Implementation Divisible Sum Pairs 문제 풀이"
date: 2017-11-14 11:18:00 +0900
tags: [hackerrank]
---

## 문제 요약

당신에게는 n개의 일차원 배열이 주어진다.

여기서 k는 (i,j)의 쌍을 나누는 수이다. (단 i < j) k로 나누었을때 나머지가 없는 쌍의 개수를 구해라.

Sample Input
```
6 3
1 3 2 6 1 2
```

Sample Output 0
```
5
```

설명

- (0,2) -> 1+2=3
- (0,5) -> 1+2=3
- (1,3) -> 3+6=9
- (2,4) -> 2+1=3
- (4,5) -> 1+2=3

## 내 소스

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
class Solution {

    static int divisibleSumPairs(int n, int k, int[] ar) {
        // Complete this function
        int totalCount = 0;
        for (int i = 0; i < n; i++)
        {
            for (int j = i+1; j < n; j++)
            {
                if ((ar[i]+ar[j])%k==0)
                {
                    totalCount++;
                }
            }
        }
        return totalCount;
    }

    static void Main(String[] args) {
        string[] tokens_n = Console.ReadLine().Split(' ');
        int n = Convert.ToInt32(tokens_n[0]);
        int k = Convert.ToInt32(tokens_n[1]);
        string[] ar_temp = Console.ReadLine().Split(' ');
        int[] ar = Array.ConvertAll(ar_temp,Int32.Parse);
        int result = divisibleSumPairs(n, k, ar);
        Console.WriteLine(result);
    }
}
```

## wanbo의 답안

```cpp
#include <bits/stdc++.h>
using namespace std;

int n, k;
int a[N];

int main() {
	cin >> n >> k;
	for(int i = 0; i < n; i++) cin >> a[i];
    
	int res = 0;
	for(int i = 0; i < n; i++) 
		for(int j = i + 1; j < n; j++) 
			if((a[i] + a[j]) % k == 0) res++;
	cout << res << endl;
	return 0;
}
```

## usinha02

문제를 풀면서 혹시 O(n)으로 푸는 방법이 존재하지 않을까 싶었는데, 역시나 존재한다.

```cpp
using namespace std;
int main(){
   
   int n;
   int k;
   cin >> n >> k;
   int a[n];
   int m[k];
   for(int i=0; i<k; i++) {
       m[i]=0;
   }

   for(int i = 0; i < n; i++) {
      cin >> a[i];
      m[a[i]%k]++;
   }
   
   int sum=0;
   sum+=(m[0]*(m[0]-1))/2;
   for(int i=1; i<=k/2 && i!=k-i; i++) {
       sum+=m[i]*m[k-i];
   }

   if(k%2==0) {
      sum+=(m[k/2]*(m[k/2]-1))/2;
   }

   cout<<sum;
   return 0;
}
```

## 느낀점

그냥 간단하게 이중 for문을 사용하여 구할 수 있다.