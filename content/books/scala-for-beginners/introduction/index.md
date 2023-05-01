---
title: "Introduction"
summary: "Why Scala?"
date: 2023-04-29T19:13:57-04:00
draft: true
authors:
    - "mattmoore"
series: ["Scala for Beginners"]
series_order: 1
---

## Why Scala?

Scala is a fascinating language. It combines concepts from both the object-oriented world as well as the functional world. You can write pure functional programs. It has a very safe type system that is highly extensible. It also runs on the JVM, allowing us to use existing Java libraries if we want.

Scala has a very concise syntax. Unlike the verbosity of Java, it allows us to express a lot of complex logic with very little code. Scala can also be used as a scripting language, or even transpile to JavaScript via ScalaJS.

When evaluating a language, I tend to look for a number of features:

1. Type System:
    1. Can I define custom types?
    2. Can I build Algebraic Data Types (ADTs)?
    3. Does the language provide a convenient method for defining ad-hoc polymorphism?
    4. Does the compiler enforce the type system consistently and reliably? How certain can I be that if I define my types correctly, the compiler will guarantee proper behavior?
2. Ease of pure functional programming:
    1. Does the lang provide a way to demarcate potentially failing operations, and provide convenient methods for handling failures?
    2. Does it allow me to define my programs in terms of contracts, and how well does it enforce those contracts? We'll examine the notion of contract-based programming in this book, but for now an example would be programming with interfaces.
    3. Does it provide a means for tail recursion?
    4. Does it provide solid and flexible pattern matching?
