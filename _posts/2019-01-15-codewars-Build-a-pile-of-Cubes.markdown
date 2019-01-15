---
layout: post
title: "[Codewars #31] Build a pile of Cubes (6kyu)"
excerpt: "[Codewars #31] Build a pile of Cubes (6kyu) 문제 풀이"
date: 2019-01-15 17:56:00 +0900
tags: [codewars, kata]
---

## Instructions

Your task is to construct a building which will be a pile of n cubes. The cube at the bottom will have a volume of n^3, the cube above will have volume of (n-1)^3 and so on until the top which will have a volume of 1^3.

You are given the total volume m of the building. Being given m can you find the number n of cubes you will have to build?

The parameter of the function findNb (find_nb, find-nb, findNb) will be an integer m and you have to return the integer n such as n^3 + (n-1)^3 + ... + 1^3 = m if such a n exists or -1 if there is no such n.


Examples:
```
findNb(1071225) --> 45
findNb(91716553919377) --> -1
mov rdi, 1071225
call find_nb ; rax <-- 45

mov rdi, 91716553919377
call find_nb ; rax <-- -1
```

## My Solution

```python
def find_nb(m):
    # your code
    sum = 0
    count = 0
    while True:
        count += 1
        sum += count**3

        if sum == m:
            return count

        if sum > m:
            return -1
```

c#으로 똑같은 방법으로 풀었는데 테스트 케이스 몇 군데서 계속 에러가 나옴.

codewars 자체가 유저들이 문제를 직접 내고, 테스트 케이스를 작성을 하는 방식이라, 이런식으로 아예 통과를 못하는 경우가 있음.

그냥 넘어가려다가 그냥 파이썬으로 똑같은 방식으로 풀자! 해서 풀었음.

하다 보니 알게 된건 파이썬에서는 증감연산자가 없다는 사실.. (++, --)등

## Best Practices

```python
def find_nb(m):
    n = 1
    volume = 0
    while volume < m:
        volume += n**3
        if volume == m:
            return n
        n += 1
    return -1
```

거의 비슷한 방식이다.