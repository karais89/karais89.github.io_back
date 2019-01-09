---
layout: post
title: "[Codewars #21] Parse HTML/CSS Colors (6kyu)"
excerpt: "[Codewars #21] Parse HTML/CSS Colors (6kyu) 문제 풀이"
date: 2019-01-09 14:05:00 +0900
tags: [codewars, kata]
---

## Instructions

In this kata you parse RGB colors represented by strings. The formats are primarily used in HTML and CSS. Your task is to implement a function which takes a color as a string and returns the parsed color as a map (see Examples).

Input:
The input string represents one of the following:

- 6-digit hexadecimal - "#RRGGBB"
e.g. "#012345", "#789abc", "#FFA077"
Each pair of digits represents a value of the channel in hexadecimal: 00 to FF
- 3-digit hexadecimal - "#RGB"
e.g. "#012", "#aaa", "#F5A"
Each digit represents a value 0 to F which translates to 2-digit hexadecimal: 0->00, 1->11, 2->22, and so on.
- Preset color name
e.g. "red", "BLUE", "LimeGreen"
You have to use the predefined map PRESET_COLORS (JavaScript, Python, Ruby), presetColors (Java, C#, Haskell), or preset-colors (Clojure). The keys are the names of preset colors in lower-case and the values are the corresponding colors in 6-digit hexadecimal (same as 1. "#RRGGBB").

```
Parse("#80FFA0") === new RGB(128, 255, 160))
Parse("#3B7") === new RGB( 51, 187, 119))
Parse("LimeGreen") === new RGB( 50, 205, 50))

// RGB struct is defined as follows:
struct RGB
{
    public byte r, g, b;
    public RGB(byte r, byte g, byte b);
}
```

## My Solution

```csharp
using System;
using System.Collections.Generic;

class HtmlColorParser
{
    private readonly IDictionary<string, string> presetColors;

    public HtmlColorParser(IDictionary<string, string> presetColors)
    {
        this.presetColors = presetColors;
    }

    public RGB Parse(string color)
    {
      string sixDigit = color;
      if (color.Substring(0, 1) == "#")
      {
        if (color.Length == 4)
        {
          string r = color.Substring(1, 1);
          string g = color.Substring(2, 1);
          string b = color.Substring(3, 1);

          sixDigit = $"#{r}{r}{g}{g}{b}{b}";
        }
      }
      else
      {
        string colorLow = color.ToLower();
        sixDigit = presetColors[colorLow];
      }

      byte[] rgb = new byte[3];
      for (int i = 0; i < rgb.Length; i++)
      {
        string hex = sixDigit.Substring((i*2) + 1, 2);
        rgb[i] = Convert.ToByte(hex, 16);
      }

      return new RGB(rgb[0], rgb[1], rgb[2]);
    }
}

```

3-digit일때 굳이 Substring으로 변환 하나하나 받아와서 사용하지 않고, 바로 인덱스에 접근하면 되는데.. 쓸데 없는 짓을 했네..

## Best Practices

```csharp
using System;
using System.Collections.Generic;

class HtmlColorParser
{
    private readonly IDictionary<string, string> presetColors;

    public HtmlColorParser(IDictionary<string, string> presetColors)
    {
        this.presetColors = presetColors;
    }

    public RGB Parse(string color)
    {
        color = color.ToLower();
        string hex;

        if (presetColors.ContainsKey(color))
            hex = presetColors[color];
        else if (color.Length == 4)
            hex = string.Format("#{0}{0}{1}{1}{2}{2}", color[1], color[2], color[3]);
        else
            hex = color;

        var n = Convert.ToInt32(hex.Substring(1), 16);
        return new RGB((byte)((n >> 16) & 0xFF), (byte)((n >> 8) & 0xFF), (byte)(n & 0xFF));
    }
}
  
```

제일 높은 표이긴 한데 표 자체가 2개 밖에 없어서 추가 검증이 필요하다.
지금 문제 푸는게 최신 문제 위주로 문제가 주어지나 싶다..

16진수를 RGB 값으로 변환할때
여기서는 그냥 바로 스트링값 전부를 int로 변환했다.
그리고 비트 연산자와 & 연산자를 사용하여 각 값들을 추출해서 사용 함. 이 부분을 좀 볼 필요가 있을 듯하다.