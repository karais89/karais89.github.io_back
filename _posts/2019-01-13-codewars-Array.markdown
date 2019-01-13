---
layout: post
title: "[Codewars #29] +1 Array (6kyu)"
excerpt: "[Codewars #29] +1 Array (6kyu) 문제 풀이"
date: 2019-01-13 14:17:00 +0900
tags: [codewars, kata]
---

## Instructions

Given an array of integers of any length, return an array that has 1 added to the value represented by the array.

- the array can't be empty
- only non-negative, single digit integers are allowed

Return nil (or your language's equivalent) for invalid inputs.

Examples

For example the array [2, 3, 9] equals 239, adding one would return the array [2, 4, 0].

[4, 3, 2, 5] would return [4, 3, 2, 6]

## My Solution

```csharp
using System;

namespace Kata
{
  public static class Kata
  {
    public static int[] UpArray(int[] num)
  {
      // invaild check
    if (num == null || num.Length == 0)
      {
        return null;
      }

      // invalid check
      for (int i = 0; i < num.Length; i++)
      {
        if (num[i] < 0 || num[i] >= 10)
        {
          return null;
        }
      }

      // array init
      int[] arrayOnePlus = new int[num.Length];
      for (int i = 0; i < arrayOnePlus.Length; i++)
      {
        arrayOnePlus[i] = num[i];
      }

      // one plus array
      for (int i = num.Length - 1; i >= 0; i--)
      {
        int n = num[i] + 1;
        if (n != 10)
        {
          arrayOnePlus[i] = n;
          break;
        }
        else
        {
          arrayOnePlus[i] = 0;

          // array size up..
          if (i == 0)
          {
            int[] newArr = new int[arrayOnePlus.Length + 1];
            newArr[0] = 1;
            for (int j = 0; j < arrayOnePlus.Length; j++)
            {
              newArr[j+1] = arrayOnePlus[j];
            }

            return newArr;
          }
        }
      }

      return arrayOnePlus;
    }
  }
}
```


원래는 int 배열을 숫자로 바꾼후에 +1을 해주고 int 배열로 다시 만드는 방식으로 구현하려다, 그냥 배열 자체에 값을 구하는 식으로 구현 하였다.

1. invaild 체크
2. 인자로 전달 받은 배열을 새 배열에 복사
3. 각 배열의 인덱스에 +1을 해준다. 그리고 만약 그 합이 10이 넘지 않으면 바로 새 배열의 값을 리턴.
4. 만약 그 합이 10이 넘는다면 루프를 돌면서 10이 넘지 않을때 까지 반복한다.
5. 마지막 배열까지 검사를 한 경우에도 10이 넘는 경우면 배열의 길이를 1 증가시켜줘야 되는 경우이므로 1을 증가 시켜준 후에 해당 배열을 다시 만들어 준다음에 리턴 해준다.

## Best Practices

```csharp
using System.Linq;

namespace Kata
{
  public static class Kata
  {
    public static int[] UpArray(int[] num)
    {
      if (num.Length == 0 || num.Any(a => a < 0 || a > 9))
        return null;

      for (var i = num.Length - 1; i >= 0; i--)
      {
        if (num[i] == 9)
        {
          num[i] = 0;
        }
        else
        {
          num[i]++;
          return num;
        }
      }
      return new []{ 1 }.Concat(num).ToArray();
    }
  }
}
```

구하는 공식은 거의 비슷하지만, 이 코드가 더 짧다.
int형 배열의 경우 레퍼런스를 넘겨주기 때문에, 배열 값 자체가 변하는 문제가 있으므로, 배열 값 변경을 원치 않는다면 새로운 배열에 값을 복사해서 진행 하면 될것 같고..
 
여기서 참고할 부분은 배열의 길이를 1 증가시켜주기 위해 새로 생성해 주는 부분.

```
int[] newArr = new int[arrayOnePlus.Length + 1];
newArr[0] = 1;
for (int j = 0; j < arrayOnePlus.Length; j++)
{
  newArr[j+1] = arrayOnePlus[j];
}
```


```
new []{ 1 }.Concat(num).ToArray();
```

Linq를 사용하면 Concat 메서드를 사용하여 이렇게 간단히 줄일 수 있다는 점은 참고할 만 한 사실이다.