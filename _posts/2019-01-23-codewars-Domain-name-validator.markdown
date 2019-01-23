---
layout: post
title: "[Codewars #44] Domain name validator (5kyu)"
excerpt: "[Codewars #44] Domain name validator (5kyu) 문제 풀이"
date: 2019-01-23 13:39:00 +0900
tags: [codewars, kata]
---

## Instructions

[링크](https://www.codewars.com/kata/5893933e1a88084be10001a3/train/csharp)

In this kata you have to create a domain name validator mostly compliant with RFC 1035, RFC 1123, and RFC 2181

For purposes of this kata, following rules apply:

- Domain name may contain subdomains (levels), hierarchically separated by . (period) character
- Domain name must not contain more than 127 levels, including top level (TLD)
- Domain name must not be longer than 253 characters (RFC specifies 255, but 2 characters are reserved for trailing dot and null character for root level)
- Level names must be composed out of lowercase and uppercase ASCII letters, digits and - (minus sign) character
- Level names must not start or end with - (minus sign) character
- Level names must not be longer than 63 characters
- Top level (TLD) must not be fully numerical

Additionally, in this kata

- Domain name must contain at least one subdomain (level) apart from TLD
- Top level validation must be naive - ie. TLDs nonexistent in IANA register are still considered valid as long as they adhere to the rules given above.
The validation function accepts string with the full domain name and returns boolean value indicating whether the domain name is valid or not.

Examples:
```
validate('codewars') == False
validate('g.co') == True
validate('codewars.com') == True
validate('CODEWARS.COM') == True
validate('sub.codewars.com') == True
validate('codewars.com-') == False
validate('.codewars.com') == False
validate('example@codewars.com') == False
validate('127.0.0.1') == False
```

## My Solution

```csharp
using System;
using System.Text.RegularExpressions;
public class DomainNameValidator {
  public bool validate(string domain) {
    Console.WriteLine(domain);
    // Domain name must not be longer than 253 characters (RFC specifies 255, but 2 characters are reserved for trailing dot and null character for root level)
    if (domain.Length > 253)
    {
      return false;
    }
    string[] domainSplitDots = domain.Split(".");
    // Domain name must contain at least one subdomain (level) apart from TLD
    if (domainSplitDots.Length <= 1)
    {
      return false;
    }
    // all integer check
    bool isAllInteger = true;
    for (int i = 0; i < domainSplitDots.Length; i++)
    {
      string str = domainSplitDots[i];
      int domainInteger = 0;
      if (!int.TryParse(str, out domainInteger))
      {
         isAllInteger = false;
      }
    }
    for (int i = 0; i < domainSplitDots.Length; i++)
    {
      string str = domainSplitDots[i];
      int domainInteger = 0;
      if (isAllInteger && int.TryParse(str, out domainInteger))
      {
        // Domain name must not contain more than 127 levels, including top level (TLD)
        if (domainInteger >= 127)
        {
          return false;
        }
      }
      else
      {
        // Level names must not be longer than 63 characters
        if (str.Length > 63)
        {
          return false;
        }
        // Level names must be composed out of lowercase and uppercase ASCII letters, digits and - (minus sign) character
        // https://stackoverflow.com/questions/1181419/verifying-that-a-string-contains-only-letters-in-c-sharp
        bool isMatch = Regex.IsMatch(str, @"^[a-zA-Z0-9-]+$");
        if (!isMatch)
        {
          Console.WriteLine(str);
          return false;
        }
        // Level names must not start or end with - (minus sign) character
        if (str.StartsWith("-") || str.EndsWith("-"))
        {
          Console.WriteLine(str);
          return false;
        }
      }
    }
    // where is rules??
    if (!isAllInteger)
    {
      string str = domainSplitDots[domainSplitDots.Length-1];
      int ret = 0;
      if (int.TryParse(str, out ret))
      {
        return false;
      }
    }
    return true;
  }
}
```

- 도메인 이름 규칙
    - 점(.) 기호로 도메인이 구분된다.
    - 레벨은 127 이상을 포함할 수 없다.
    - 도메인 이름은 253 글자보다 반드시 작아야 한다.
    - 레벨 이름은 소문자, 대문자, 아스키 문자, 숫자 및 대쉬(-) 문자로 구성되어야 한다.
    - 레벨 이름은 대쉬(-)로 시작하거나 끝날 수 없다.
    - 레벨 이름은 63 글자를 초과할 수 없다.
    - 최상위 레벨은 완전히 숫자여야 한다.
    - 도메인 이름은 TLD를 제외한 하나 이상의 하위 도메인이 포함되어야 한다.
    - 최상위 유효성 검사는 순진해야합니다. 즉. IANA 등록부에 존재하지 않는 TLD는 위에 주어진 규칙을 준수하는 한 유효한 것으로 간주됩니다.
- 문제를 잘못 이해해서 잘못 푼 부분이 있다.
- 127 leves 부분을 잘못 이해 했음.

> 정규 표현식은 나중에 포스트로 정리를 해야 겠다.

## Best Practices

```csharp
using System;
using System.Text.RegularExpressions;
public class DomainNameValidator {
  public bool validate(string domain) {
    if (domain.Length > 253)
      return false;
    Regex re = new Regex(@"^(?!-)[a-z0-9-]{1,63}(?<!-)(?:\.(?!-)[a-z0-9-]{1,63}(?<!-)){0,125}\.(?!-)(?![0-9]+$)[a-z0-9-]{1,63}(?<!-)$", RegexOptions.IgnoreCase);
    Match m = re.Match(domain);
    return m.Success;
  }
}
```

- 정규 표현식 관련 문제를 많이 푸는 것 같다.
- 도메인 규칙같은건 확실히 정규표현식을 사용하는게 맞다.

## g0dm0d3's Solution

```csharp
using System;
using System.Text.RegularExpressions;
using System.Linq;
public class DomainNameValidator {
  public bool validate(string domain) {
    if (domain.Length < 3 || domain.Length > 253) return false;
    var levels = domain.Split('.');
    if (levels.Count() < 2 || levels.Count() > 127) return false;
    var zone = levels.Last();
    if (Regex.IsMatch(zone, @"^[0-9]+$")) return false;
    foreach (var level in levels) {
      if (level.Length > 63) return false;
      if (!Regex.IsMatch(level, @"^[a-zA-Z0-9\-]+$")) return false;
      if (level.StartsWith("-") || level.EndsWith("-")) return false;
    }
    return true;
  }
}
```

- 표 자체는 받지 못한 해결책이다.
- 그나마 가장 이해하기 쉬운 코드인것 같아서 가져왔다.