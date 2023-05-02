---
title: "Type Classes"
summary: "Type Classes are a way for us to do ad-hoc polymorphism. Don't know what ad-hoc polymorphism is? No worries, we'll cover that in this chapter."
date: 2023-04-29T19:15:22-04:00
draft: true
authors:
    - "mattmoore"
series: ["Scala for Beginners"]
series_order: 6
---

## What is a type class?

A type class is a method for achieving what is called "ad-hoc polymorphism".

### Ad-hoc polymorphism

```scala {linenos=table}
//> using scala "3.2.2"

// Type Class
trait Show[A] {
  extension(a: A) def show: String
}

case class Person(firstName: String, lastName: String)

// Type Class instance via given
given Show[Person] with {
  extension(p: Person) def show: String =
    s"${p.firstName} ${p.lastName}"
}

@main
def main(args: String*) = {
  val person = Person("Matt", "Moore")
  println(person.show)
}
```
