---
title: "Chapter 4: Pattern Matching"
draft: true
authors:
    - "mattmoore"
books: ["Scala for Beginners"]
---

## Pattern Matching

{{<highlight scala "linenos=table">}}
//> using scala "3.2.2"

def testMatches(list: List[Int]): String =
    list match
        case Nil => "Empty list"
        case head +: Nil  => "Has one item"
        case _ :+ tail    => s"Last item is $tail"
        case head :: next => "Full list"
    

@main
def main(args: String*) = {
    println(testMatches(List()))
    println(testMatches(List(1)))
    println(testMatches(List(1, 2)))
}
{{</highlight>}}