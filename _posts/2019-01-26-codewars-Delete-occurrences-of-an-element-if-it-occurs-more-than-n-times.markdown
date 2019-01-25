---
layout: post
title: "[Codewars #54] Delete occurrences of an element if it occurs more than n times (6kyu)"
excerpt: "[Codewars #54] Delete occurrences of an element if it occurs more than n times (6kyu) 문제 풀이"
date: 2019-01-26 01:58:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/554ca54ffa7d91b236000023/train/csharp)

Enough is enough!
Alice and Bob were on a holiday. Both of them took many pictures of the places they've been, and now they want to show Charlie their entire collection. However, Charlie doesn't like this sessions, since the motive usually repeats. He isn't fond of seeing the Eiffel tower 40 times. He tells them that he will only sit during the session if they show the same motive at most N times. Luckily, Alice and Bob are able to encode the motive as a number. Can you help them to remove numbers such that their list contains each number only up to N times, without changing the order?

Task
Given a list lst and a number N, create a new list that contains each number of lst at most N times without reordering. For example if N = 2, and the input is [1,2,3,1,2,1,2,3], you take [1,2,3,1,2], drop the next [1,2] since this would lead to 1 and 2 being in the result 3 times, and then take 3, which leads to [1,2,3,1,2,3].

Example
```
Kata.DeleteNth (new int[] {20,37,20,21}, 1) // return [20,37,21]
Kata.DeleteNth (new int[] {1,1,3,3,7,2,2,2,2}, 3) // return 
```

## My Solution

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Kata {
  public static int[] DeleteNth(int[] arr, int x) {
    Dictionary<int, int> valCounts = new Dictionary<int, int>();
    List<int> removeArrs = new List<int>();
    for (int i = 0; i < arr.Length; i++)
    {
      if (valCounts.ContainsKey(arr[i]))
      {
        if (++valCounts[arr[i]] <= x)
        {
          removeArrs.Add(arr[i]);
        }
      }
      else
      {
        valCounts[arr[i]] = 1;
        removeArrs.Add(arr[i]);
      }
    }
    return removeArrs.ToArray();
  }
}
```

- 여러번 반복하는걸 제거해 달라는 요구 사항.
- Dictinary를 사용하여 해당 되는 키 값이 없으면 1 있으면 +1 해주어서 x보다 작을때만 리스트에 더해준 후 해당 리스트 반환
- Linq를 사용하면 더 간단히 풀리겠지...

## Best Practices

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Kata {
  public static int[] DeleteNth(int[] arr, int x) {
    var result = new List<int>();
    foreach(var item in arr) {
      if(result.Count(i => i == item) < x)
        result.Add(item);
    }
    return result.ToArray();
  }
}
```

- C# 문제는 거의 모두 linq를 사용하여 푼다고 보면 될 듯.
- Linq중 Count 함수를 사용하여 해당되는 값이 몇개 있는지 반환한후 그것보다 작을시에만 더해주었다.
- 확실히 내 해결책  보다 훨씬 간단하다.