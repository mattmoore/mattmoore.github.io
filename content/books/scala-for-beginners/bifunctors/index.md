---
title: "Bifunctors"
date: 2023-04-30T11:22:35-04:00
draft: true
series: ["Scala for Beginners"]
series_order: 7
---

## Either is a bifunctor

We've looked at `Either` already. It is an <abbr title="Algebraic Data Type">ADT</abbr> that can model success or failure cases. It models two possible outcomes. This ability for it to model two outcomes is what makes it a bifunctor.

```go {linenos=table}
//> using scala "3.2.2"
//> using dep "org.typelevel::cats-core:2.9.0"

import cats.*
import cats.syntax.all.*

val records: List[(Int, Int)] = List(
    (450000, 3),
    (770000, 3),
    (990000, 2),
    (2100, 4),
    (433000, 3)
)

def calculateContributionPerMonth(balance: Int, lifetime: Int) =
    balance / lifetime

val result: List[Int] =
    records.map(
        record => record.bimap(
            cents => cents / 100,
            years => 12 * years
        )
    ).map((calculateContributionPerMonth _).tupled)


case class Person(name: String)
val people = List(
    (1, Person("Matt")),
    (2, Person("Mary"))
)

val peopleResult =
    people.map(
        person => person.bimap(
            id => s"P:$id",
            person => s"Hello ${person.name}"
        )
    )

@main
def main(args: String*) =
    println(result)
    println(peopleResult)
```