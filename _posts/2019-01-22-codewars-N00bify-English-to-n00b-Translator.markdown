---
layout: post
title: "[Codewars #42] N00bify - English to n00b Translator (5kyu)"
excerpt: "[Codewars #42] N00bify - English to n00b Translator (5kyu) 문제 풀이"
date: 2019-01-22 19:46:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/n00bify-english-to-n00b-translator/train/csharp)

The internet is a very confounding place for some adults. Tom has just joined an online forum and is trying to fit in with all the teens and tweens. It seems like they're speaking in another language! Help Tom fit in by translating his well-formatted English into n00b language.

The following rules should be observed:

- "to" and "too" should be replaced by the number 2, even if they are only part of a word (E.g. today = 2day)
- Likewise, "for" and "fore" should be replaced by the number 4
- Any remaining double o's should be replaced with zeros (E.g. noob = n00b)
- "be", "are", "you", "please", "people", "really", "have", and "know" should be changed to "b", "r", "u", "plz", "ppl", "rly", "haz", and "no" respectively (even if they are only part of the word)
- When replacing words, always maintain case of the first letter unless another rule forces the word to all caps.
- The letter "s" should always be replaced by a "z", maintaining case
- "LOL" must be added to the beginning of any input string starting with a "w" or "W"
- "OMG" must be added to the beginning (after LOL, if applicable,) of a string 32 characters(1) or longer
- All evenly numbered words(2) must be in ALL CAPS (Example: Cake is very delicious. becomes Cake IZ very DELICIOUZ)
- If the input string starts with "h" or "H", the entire output string should be in ALL CAPS
- Periods ( . ), commas ( , ), and apostrophes ( ' ) are to be removed
- (3)A question mark ( ? ) should have more question marks added to it, equal to the number of words2 in the sentence (Example: Are you a foo? has 4 words, so it would be converted to r U a F00????)
- (3)Similarly, exclamation points ( ! ) should be replaced by a series of alternating exclamation points and the number 1, equal to the number of words(2) in the sentence (Example: You are a foo! becomes u R a F00!1!1)

1 Characters should be counted After: any word conversions, adding additional words, and removing punctuation. Excluding: All punctuation and any 1's added after exclamation marks ( ! ). Character count includes spaces.

2 For the sake of this kata, "words" are simply a space-delimited substring, regardless of its characters. Since the output may have a different number of words than the input, words should be counted based on the output string.

Example: whoa, you are my 123 <3 becomes LOL WHOA u R my 123 <3 = 7 words

3 The incoming string will be punctuated properly, so punctuation does not need to be validated.

## My Solution

```csharp

using System;
using System.Text;
using System.Text.RegularExpressions;

public static class Kata
{
  public static string N00bify(string text)
  {
    Console.WriteLine(text);
    // 딱 봐도 정규표현식을 사용해서 풀어야 되는 문제 어거지로 하면 풀 수 야 있을거 같은데.. 의미가 있나?
    // 대소문자 유지 규칙이 있어서 더 더럽다.

    // 규칙 1 : "to" and "too" should be replaced by the number 2, even if they are only part of a word (E.g. today = 2day)
    string convStr = Regex.Replace(text, "too", "2", RegexOptions.IgnoreCase);
    convStr = Regex.Replace(convStr, "to", "2", RegexOptions.IgnoreCase);

    // 규칙 2 : Likewise, "for" and "fore" should be replaced by the number 4
    convStr = Regex.Replace(convStr, "fore", "4", RegexOptions.IgnoreCase);
    convStr = Regex.Replace(convStr, "for", "4", RegexOptions.IgnoreCase);

    // 규칙 3 : Any remaining double o's should be replaced with zeros (E.g. noob = n00b)
    convStr = convStr.Replace("Oo", "00").Replace("oo", "00");

    // 규칙 4 : "be", "are", "you", "please", "people", "really", "have", and "know" should be changed to "b", "r", "u", "plz", "ppl", "rly", "haz", and "no" respectively (even if they are only part of the word)
    convStr = Regex.Replace(convStr, "be", "b", RegexOptions.IgnoreCase);
    convStr = Regex.Replace(convStr, "are", "r", RegexOptions.IgnoreCase);
    convStr = Regex.Replace(convStr, "you", "u", RegexOptions.IgnoreCase);
    convStr = Regex.Replace(convStr, "please", "plz", RegexOptions.IgnoreCase);
    convStr = Regex.Replace(convStr, "people", "ppl", RegexOptions.IgnoreCase);
    convStr = Regex.Replace(convStr, "really", "rly", RegexOptions.IgnoreCase);
    convStr = Regex.Replace(convStr, "have", "haz", RegexOptions.IgnoreCase);
    convStr = Regex.Replace(convStr, "know", "no", RegexOptions.IgnoreCase);

    // 규칙 5 : When replacing words, always maintain case of the first letter unless another rule forces the word to all caps.

    // 규칙 6 : The letter "s" should always be replaced by a "z", maintaining case
    convStr = convStr.Replace("s", "z").Replace("S", "Z");


    // 규칙 11 : Periods ( . ), commas ( , ), and apostrophes ( ' ) are to be removed
    convStr = convStr.Replace(".","").Replace(",","").Replace("'","");

    // 규칙 7 : "LOL" must be added to the beginning of any input string starting with a "w" or "W"
    if (text[0] == 'w' || text[0] == 'W')
    {
      convStr = "LOL " + convStr;
    }

    // 규칙 8 : "OMG" must be added to the beginning (after LOL, if applicable,) of a string 32 characters1 or longer
    // Console.WriteLine("convStr: " + convStr + " " + convStr.Length);
    int markCount = 0;
    for (int i = 0; i < convStr.Length; i++)
    {
      if (convStr[i] == '!')
      {
        markCount++;
      }
    }

    int convStrLength = convStr.Length - markCount;
    if (convStrLength >= 32)
    {
      if (convStr.StartsWith("LOL"))
      {
        convStr = convStr.Replace("LOL", "LOL OMG");
      }
      else
      {
        convStr = "OMG " + convStr;
      }
    }

    // 규칙 10 : If the input string starts with "h" or "H", the entire output string should be in ALL CAPS
    if (text[0] == 'h' || text[0] == 'H')
    {
      convStr = convStr.ToUpper();
    }
    else
    {
      // 규칙 9 : All evenly numbered words2 must be in ALL CAPS (Example: Cake is very delicious. becomes Cake IZ very DELICIOUZ)
      string[] convStrSplits = convStr.Split();
      StringBuilder strBuilder = new StringBuilder();
      for (int i = 0; i < convStrSplits.Length; i++)
      {
        string str = convStrSplits[i];
        if (i % 2 != 0)
        {
          str = str.ToUpper();
        }
        strBuilder.Append(str);

        if (i != convStrSplits.Length - 1)
        {
          strBuilder.Append(" ");
        }
      }
      convStr = strBuilder.ToString();
    }

    // 규칙 12 : 3A question mark ( ? ) should have more question marks added to it, equal to the number of words2 in the sentence (Example: Are you a foo? has 4 words, so it would be converted to r U a F00????)
    int wordCount = convStr.Split().Length;
    StringBuilder sBuilder = new StringBuilder();
    for (int i = 0; i < wordCount; i++)
    {
      sBuilder.Append("?");
    }
    convStr = convStr.Replace("?", sBuilder.ToString());

    sBuilder.Clear();
    // 규칙 13 : Similarly, exclamation points ( ! ) should be replaced by a series of alternating exclamation points and the number 1, equal to the number of words2 in the sentence (Example: You are a foo! becomes u R a F00!1!1)
    for (int i = 0; i < wordCount; i++)
    {
      if (i % 2 == 0)
      {
        sBuilder.Append("!");
      }
      else
      {
        sBuilder.Append("1");
      }
    }
    convStr = convStr.Replace("!", sBuilder.ToString());

    return convStr;
  }
}
```


- 일반 문장을 인터넷 용어로 변경하는 문제
- 문제가 복잡하기 보다는, 조건 자체가 많다.
- 딱 봐도 정규표현식을 사용해서 풀어야 되는 문제..
- "to", "too"는 숫자 2로 대체
- "for", "fore"는 숫자 4로 대체
- 연속해서 2번 나오는 문자 o의 경우 숫자 0으로 대체
- "be", "are", "you", "please", "people", "really", "have", "know" 는 각각 "b", "r", "u", "plz", "ppl", "rly", "haz", "no"로 변경한다. (그들이 단어의 일부일지라도)
- 단어를 대체 할 때 다른 규칙이 단어를 모두 대문자로 사용하지 않는 한 항상 첫 번째 문자의 대 / 소문자를 유지하십시오.
- 문자 "s"는 항상 "z"로 대치되어야합니다.
- "LOL"은 "w"또는 "W"로 시작하는 입력 문자열의 시작 부분에 추가되어야합니다.
- "OMG"는 32 문자 이상의 문자열 시작 부분 (LOL 이후)에 추가해야합니다
- 짝수 번째 단어의 경우 모두 대문자 여야합니다 (예 : Cake is very delicious. -> Cake IZ very DELICIOUZ)
- 입력 문자열이 "h"또는 "H"로 시작하면 전체 출력 문자열은 모두 대문자 여야합니다
- 마침표 (.), 쉼표 (,) 및 아포스트로피 ( ')는 제거됩니다.
- 물음표 (?)에 더 많은 물음표가 추가되어야합니다 (예 : Are you a foo? 는 4 단어로 구성되어 있으므로 다음과 같이 변경합니다 r U a F00????)
- 유사하게, 느낌표 (!)는 일련의 대체 느낌표와 문장에서 단어의 수와 같은 숫자로 대체해야합니다 (예 : You are a foo! becomes u R a F00!1!1)


## Best Practices

```csharp
using System;
using System.Text.RegularExpressions;
using System.Collections.Generic;
using System.Linq;

public static class Kata
{
  private static readonly Dictionary<string, string> Replacements = new Dictionary<string, string>
  {
      {"too", "2"},
      {"Too", "2"},
      {"to", "2"},
      {"To", "2"},
      {"be", "b"},
      {"Be", "B"},
      {"fore", "4"},
      {"FORE", "4"},
      {"for", "4"},
      {"oo", "00"},
      {"OO", "00"},
      {"Oo", "00"},
      {"are", "r"},
      {"you", "u"},
      {"You", "u"},
      {"please", "plz"},
      {"people", "ppl"},
      {"really", "rly"},
      {"have", "haz"},
      {"know", "no"},
      {"s", "z"},
      {"S", "Z"},
      {".", ""},
      {",", ""},
      {"'", ""}
  };

  private static readonly char[] SentenceDelimiters = {'.', '!', '?'};


  public static string N00bify(string text)
  {
      var result = string.Empty;
      var sentences = Regex.Split(text, @"(?<=[.!?])\s+(?=\p{Lt})");
      foreach (var sentence in sentences)
      {
          var currentSentence = sentence;
          currentSentence = MapPatterns(currentSentence);
          currentSentence = UpperCaseIfStartsWithH(currentSentence);

          var lengthBeforeInsertions = currentSentence.Count(c => !SentenceDelimiters.Any(d => d == c));
          var needLol = currentSentence.Trim().ToLowerInvariant().StartsWith("w");
          var needOmg = lengthBeforeInsertions + (needLol ? 4 : 0) >= 32;
          currentSentence = InsertOmgLol(needOmg, currentSentence, needLol);

          var wordCount = CountWords(currentSentence);
          currentSentence = InsertQuestions(currentSentence, wordCount);
          currentSentence = InsertExclamations(currentSentence, wordCount);

          var wordsOriginal = currentSentence.Trim().Split(' ');
          var wordsModified = wordsOriginal.Select((w, i) => i%2 == 1 ? w.ToUpperInvariant() : w).ToArray();
          currentSentence = string.Join(" ", wordsModified);
          result += currentSentence;
      }
      return result;
  }

  private static string InsertExclamations(string _sentence, int wordCount)
  {
      return _sentence.Replace("!",
          string.Concat(Enumerable.Range(0, wordCount).Select(i => i%2 == 0 ? "!" : "1").ToArray()));
  }

  private static string InsertQuestions(string _sentence, int wordCount)
  {
      _sentence = _sentence.Replace("?", new string('?', wordCount));
      return _sentence;
  }

  private static int CountWords(string _sentence)
  {
      var wordCount = _sentence.Trim().Split(' ').Select(w => !string.IsNullOrWhiteSpace(w)).Count();
      return wordCount;
  }

  private static string InsertOmgLol(bool needOmg, string _sentence, bool needLol)
  {
      if (needOmg)
          _sentence = "OMG " + _sentence;
      if (needLol)
          _sentence = "LOL " + _sentence;
      return _sentence;
  }

  private static string UpperCaseIfStartsWithH(string _sentence)
  {
      if (_sentence.Trim().ToLowerInvariant().StartsWith("h"))
          _sentence = _sentence.ToUpperInvariant();
      return _sentence;
  }

  private static string MapPatterns(string _sentence)
  {
      foreach (var replacement in Replacements)
          _sentence = _sentence.Replace(replacement.Key, replacement.Value);
      return _sentence;
  }
}
```

- 정규표현식 모르면 거의 못푸는 느낌..
- 이정도까지 문자열을 변환 시키고 싶으면, 확실히 정규표현식을 알아야 될 듯.