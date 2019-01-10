---
layout: post
title: "[Codewars #23] Sum The Tree (6kyu)"
excerpt: "[Codewars #23] Sum The Tree (6kyu) 문제 풀이"
date: 2019-01-10 22:21:00 +0900
tags: [codewars, kata]
---

## Instructions

Given a node object representing a binary tree:

```
Node:
  value: <int>,
  left: <Node> or null,
  right: <Node> or null

Node:
  value: <int>,
  left: <Node> or null,
  right: <Node> or null

struct node
{
  int value;
  node* left;
  node* right;
}

public class Node
{
    public int Value;
    public Node Left;
    public Node Right;

    public Node(int value, Node left = null, Node right = null)
    {
      Value = value;
      Left = left;
      Right = right;
    }
}

```


write a function that returns the sum of all values, including the root. Absence of a node will be indicated with a null value.

```
10
| \
1 2
=> 13

1
| \
0 0
    \
     2
=> 3
```

## My Solution

```csharp
using System;
using System.Collections.Generic;

public static class Kata
{
  public static int SumTree(Node root)
  {
    return RecusiveTree(root);
  }

  public static int RecusiveTree(Node root)
  {
    if (root == null)
    {
      return 0;
    }

    if (root.Left == null && root.Right == null)
    {
      return root.Value;
    }

    if (root.Left != null && root.Right != null)
    {
      return root.Value + RecusiveTree(root.Left) + RecusiveTree(root.Right);
    }
    else if (root.Right != null)
    {
      return root.Value + RecusiveTree(root.Right);
    }
    else if (root.Left != null)
    {
      return root.Value + RecusiveTree(root.Left);
    }
    return 0;
  }
}
```


전체 트리의 합 구하는 문제

트리의 합은 재귀 호출 밖에 없나?? 라는 생각을 하면서 재귀 호출을 사용하여 구하였다..


## Best Practices

```csharp
using System;

public static class Kata
{
  public static int SumTree(Node root)
  {
    return root == null ? 0 : root.Value + SumTree(root.Left) + SumTree(root.Right);
  }
}
```

재귀 호출을 사용하여 해결 한줄로 짤 수 있는 코드 였구나..

쓸데 없는 비교 검사문등을 집어 넣어서, 코드가 길어 졌다. 반성 하자.

재귀 호출을 사용하지 않고 해결하는 방법

```
public static void preTraverseNoRec(Node root){
    Stack<Node> stack = new Stack<eNode>();
    stack.push(root);
    while(stack.size()!=0) {
        Node node = stack.pop();
        System.out.println(node.data);
        if(node.right != null) stack.push(node.right);
        if(node.left != null) stack.push(node.left);
    }
}
```