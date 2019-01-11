---
layout: post
title: "[Codewars #26] extract file name (6kyu)"
excerpt: "[Codewars #26] extract file name (6kyu) 문제 풀이"
date: 2019-01-11 23:45:00 +0900
tags: [codewars, kata]
---

## Instructions

You have to extract a portion of the file name as follows:

- Assume it will start with date represented as long number
- Followed by an underscore
- Youll have then a filename with an extension
- it will always have an extra extension at the end

Inputs:
```
1231231223123131_FILE_NAME.EXTENSION.OTHEREXTENSION

1_This_is_an_otherExample.mpg.OTHEREXTENSIONadasdassdassds34

1231231223123131_myFile.tar.gz2
```

Outputs:
```
FILE_NAME.EXTENSION

This_is_an_otherExample.mpg

myFile.tar
```

Acceptable characters for random tests:

```
abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ_-0123456789
```

The recommend way to solve it is using RegEx and specifically groups.

## My Solution

```csharp
using System;

namespace Solution
{
    class FileNameExtractor
    {
        public static bool IsOnlyNumber(string str)
        {
          foreach (char s in str)
          {
            if (s < '0' || s > '9')
            {
              return false;
            }
          }
          return true;
        }

        public static string ExtractFileName(string dirtFileName)
        {
          // 앞에 숫자 제외 뒤에 확장자 제거 하고 반환?
          string fileName = dirtFileName;
          string[] underSplit = dirtFileName.Split('_');
          string firstSplit = underSplit[0];
          if (IsOnlyNumber(firstSplit))
          {
            // number remove
            int index = fileName.IndexOf('_');
            if (index != -1)
            {
              fileName = fileName.Substring(index + 1);
            }
          }

          // extension remove
          int exIndex = fileName.LastIndexOf('.');
          if (exIndex != -1)
          {
            fileName = fileName.Substring(0, exIndex);
          }

          // Your code here
          return fileName;
        }
    }
}
```

문제 자체에 아예 정규 표현식을 사용하라고 나와있다.

하지만 나는 당당히.. 정규 표현식을 사용하지 않고 풀어야지..

해결 방법은 간단하다.

1. 언더스코어(_) 기준으로 문자열을 쪼갠다음에 맨 첫번째 문자가 숫자로 구성되어 있으면 제거 해준다.
2. 점(.) 기준으로 또 문자열을 찾은다음에 찾은 문자열 다음에는 제거 해준다.
3. 구한 값을 리턴 해준다.

## Best Practices

```csharp
using System.Text.RegularExpressions;

public class FileNameExtractor
{
    public static string ExtractFileName(string s) => Regex.Match(s, @"(?<=_).+(?=\.)").Value;
}
```

정규 표현식을 쓰면 한줄이면 해결 할 수 있다!