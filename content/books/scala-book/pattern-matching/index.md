---
title: "Pattern Matching"
summary: "Scala comes with first class support for pattern matching - a very powerful tool."
date: 2023-04-29T19:14:43-04:00
draft: true
authors:
    - "mattmoore"
series: ["Scala Book"]
series_order: 5
---

## Introduction to pattern matching

Scala has first-class support for pattern matching.

### Guard clauses

```scala {linenos=table}

```

### Matching on lists

```scala {linenos=table}
//> using scala "3.2.2"

def testMatches(list: List[Int]): String =
    list match
        case Nil => "Empty list"
        case head +: Nil  => "Has one item"
        case _ :+ tail    => s"Last item is $tail"
        case head :: next => "Full list"

@main
def main(args: String*) = {
    List(
        testMatches(List()),
        testMatches(List(1)),
        testMatches(List(1, 2))
    ).foreach(println)
}
```
