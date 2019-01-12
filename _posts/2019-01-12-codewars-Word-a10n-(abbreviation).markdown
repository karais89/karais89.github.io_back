---
layout: post
title: "[Codewars #27] Word a10n (abbreviation) (6kyu)"
excerpt: "[Codewars #27] Word a10n (abbreviation) (6kyu) 문제 풀이"
date: 2019-01-12 23:54:00 +0900
tags: [codewars, kata]
---

## Instructions

The word i18n is a common abbreviation of internationalization in the developer community, used instead of typing the whole word and trying to spell it correctly. Similarly, a11y is an abbreviation of accessibility.

Write a function that takes a string and turns any and all "words" (see below) within that string of length 4 or greater into an abbreviation, following these rules:

- A "word" is a sequence of alphabetical characters. By this definition, any other character like a space or hyphen (eg. "elephant-ride") will split up a series of letters into two words (eg. "elephant" and "ride").

- The abbreviated version of the word should have the first letter, then the number of removed characters, then the last letter (eg. "elephant ride" => "e6t r2e").

```
abbreviate("elephant-rides are really fun!")
// ^^^^^^^^*^^^^^*^^^*^^^^^^*^^^*
// words (^): "elephant" "rides" "are" "really" "fun"
// 123456 123 1 1234 1
// ignore short words: X X

// abbreviate: "e6t" "r3s" "are" "r4y" "fun"
// all non-word characters (*) remain in place
// "-" " " " " " " "!"
=== "e6t-r3s are r4y fun!"
```

## My Solution

```csharp
using System;
using System.Text;

public class Abbreviator
    {
        public static string Abbreviate(string input)
        {
            StringBuilder strBuilder = new StringBuilder();
            StringBuilder wordBuilder = new StringBuilder();
            for (int i = 0; i < input.Length; i++)
            {
              // STEP 1: 기호를 만나거나 맨 마지막 루프를만날때까지 계속해서 단어를 더해준다.
              if (Char.IsLetter(input[i]))
              {
                wordBuilder.Append(input[i]);
              }

              // STEP 2: 그게 아니라면 단어의 축약형을 만들어 준다.
              if (!Char.IsLetter(input[i]) ||
                  (Char.IsLetter(input[i]) && i == input.Length - 1))
              {
                // 축약 만들기
                string word = wordBuilder.ToString();
                if (word.Length >= 4)
                {
                  word = $"{word[0]}{word.Length-2}{word[word.Length-1]}";
                }

                // 최종 단어를 builder에 넣어주기
                if (!Char.IsLetter(input[i]))
                {
                  word += input[i];
                }

                strBuilder.Append(word);
                wordBuilder.Clear();
              }
            }

            return strBuilder.ToString();
        }
    }
```


처음에 문제를 잘 못 이해해서 다시 풀었다.

문제는 단어의 축약형을 만드는 것이고, 문자를 제외한 모든 기호의 경우 기호를 기준으로 2개의 문장으로 나누어 진다.

문장의 경우 4글자 이상인 경우에만 축약형을 만들고, 그렇지 않다면 문장 그대로를 사용 한다.

1. for 루프 문에서 문자인 경우 wordBuilder에 모두 집어 넣는다.
2. 그 다음에는 문자가 아니 거나, 맨 마지막 단어가 문자일 경우에 축약형을 만들 준비를 한다.

아래는 축약형 로직
1. 문자열을 길이를 받아와 4 이하라면 무조건 리턴
2. 맨 처음 문자열의 문자 + 문자열의 길이 - 2 + 마지막 문자열의 문자

문제를 풀면서도 정규 표현식을 사용하면 엄청 쉽게 풀 수 있을 것 같다는 예감을 받았다. 그 예감은 역시나 들어맞았다.

## Best Practices

```csharp
using System;
using System.Linq;
using System.Text.RegularExpressions;

public class Abbreviator
    {
        public static string Abbreviate(string input)
        {
            return Regex.Replace(input, @"\w{4,}", m => String.Concat(m.ToString().First(), m.ToString().Count() - 2, m.ToString().Last()));
        }
    }
```

정규 표현식을 쓰면 한줄이면 해결 할 수 있다!