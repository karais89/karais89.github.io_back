---
layout: post
title: "[Codewars #36] Basic DeNico (5kyu)"
excerpt: "[Codewars #36] Basic DeNico (5kyu) 문제 풀이"
date: 2019-01-19 00:13:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/basic-denico/train/csharp)

Description:
Task
Write a function deNico/de_nico() that accepts two parameters:

- key/$key - string consists of unique letters and digits
- message/$message - string with encoded message
and decodes the message using the key.

First create a numeric key basing on the provided key by assigning each letter position in which it is located after setting the letters from key in an alphabetical order.

For example, for the key crazy we will get 23154 because of acryz (sorted letters from the key).
Let's decode cseerntiofarmit on using our crazy key.

```
1 2 3 4 5
---------
c s e e r
n t i o f
a r m i t
  o n
```

After using the key:

```
2 3 1 5 4
---------
s e c r e
t i n f o
r m a t i
o n
```

Notes
- The message is never shorter than the key.
- Don't forget to remove trailing whitespace after decoding the message

Examples
```
deNico("crazy", "cseerntiofarmit on ") => "secretinformation"
deNico("abc", "abcd") => "abcd"
deNico("ba", "2143658709") => "1234567890"
deNico("key", "eky") => "key"
Check the test cases for more examples.
```

Related Kata
Basic Nico - encode

## My Solution

```csharp
using System;
using System.Text;
using System.Collections.Generic;

public class Kata {

  public static List<int> GetOrdersByKey(string key)
  {
    char[] charKeys = key.ToCharArray();
    List<char> sortCharKeys = new List<char>(charKeys);
    sortCharKeys.Sort();

    List<int> orders = new List<int>();
    for (int i = 0; i < charKeys.Length; i++)
    {
      for (int j = 0; j < sortCharKeys.Count; j++)
      {
        if (charKeys[i] == sortCharKeys[j])
        {
          if (!orders.Contains(j))
          {
            orders.Add(j);
          }
        }
      }
    }

    return orders;
  }

  public static string ConvMessageToKey(List<int> orders, string message)
  {
    if (orders == null || orders.Count == 0)
    {
      return message;
    }

    int subLength = orders.Count;
    StringBuilder strBuilder = new StringBuilder();
    StringBuilder subStrBuilder = new StringBuilder();
    for (int i = 0; i < message.Length; i += subLength)
    {
      string subStr = (i + subLength < message.Length) ? (message.Substring(i, subLength)) : (message.Substring(i));
      subStrBuilder.Clear();
      for (int j = 0; j < orders.Count; j++)
      {
        int idx = orders[j];
        if (idx < subStr.Length)
        {
          subStrBuilder.Append(subStr[idx]);
        }
      }
      strBuilder.Append(subStrBuilder.ToString().Trim());
    }

    return strBuilder.ToString();
  }

  public static string DeNico(string key, string message)
  {
    // step 1 : key string sorting number
    List<int> orders = GetOrdersByKey(key);

    // step 2: message convert orders
    string convMsg = ConvMessageToKey(orders, message);

    return convMsg;
  }
}
```

- 주어진 key에 대해서 알파벳 순서로 정렬한 key를 구하고 해당 인덱스에 각 key 값의 인덱스를 대입하여 새로운 int형 key를 만든다.
- 이 int형 리스트를 사용하여 주어진 message를 변환해준다.

> 이 문제를 풀고 5 kyu가 되었다.

## Best Practices

```csharp
using System.Linq;
using System;

public class Kata {
  public static string DeNico(string key, string m) {
      int [] coder = key.OrderBy(x=>x).Select(e=> key.IndexOf(e)).ToArray();
      return string.Concat(m.Select((e,i)=>m[ ((int)(i/ key.Length))*key.Length + Array.IndexOf(coder,i%key.Length)])).TrimEnd(' ');;
  }
}
```

이걸 보면 Linq를 배워야 되겠다
2줄이면 해결이 되네..


- GetOrdersByKey 함수는 int [] coder = key.OrderBy(x=>x).Select(e=> key.IndexOf(e)).ToArray();
이렇게 한줄이면 해결이 된다.
- ConvMessageToKey 함수는 string.Concat(m.Select((e,i)=>m[ ((int)(i/ key.Length))*key.Length + Array.IndexOf(coder,i%key.Length)])).TrimEnd(' '); 이거 한줄이면 해결이 된다.