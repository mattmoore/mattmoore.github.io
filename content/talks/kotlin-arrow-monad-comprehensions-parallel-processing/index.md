---
title: "FP with Kotlin/Arrow: Monad Comprehensions & Parallel Processing"
summary: "Kotlin is a great language! But what if you want to call an existing native library in Kotlin, or build your own native library for performance optimizations? This is also useful for making Kotlin a viable option for systems programming. Perhaps there’s an existing C or C++ library that would be great to have available in Kotlin, but it isn’t yet Kotlin-ready? We’ll take a look at how to access native libraries with Kotlin."
date: 2020-02-11
draft: false
menu:
    main:
        parent: "Talks"
        weight: 30
---

{{< youtube nAtzuIRryuE >}}

[Slides](https://speakerdeck.com/mattmoore/arrow-monad-comprehensions-and-parallel-processing)

Monads make our code safer, provide better guarantees with error handling, and clean our code up to be easier to read. In this talk we’ll take a look at Arrow’s IO monad, and how we can combine it with Either<Left, Right>.

When composing multiple monads you’ll start to notice that you end up indenting a lot of code as you begin to nest flatMaps into flatMaps with multiple monads for each step in the happy path. We’ll take a look at another strategy for handling monadic composition in Arrow with monad comprehensions.

Monad comprehensions give us the flexibility to make our IO operations a little easier to navigate by writing each sequential operation line by line, making our code look more imperative, while still executing in a functional manner. And we still get to keep all the safety guarantees that monads provide in the process. We’ll also see how monad comprehensions were inspired by Haskell’s do notation and Scala’s for comprehensions.

Next, we’ll examine how to handle parallel processing the functional way with parallel map strategies combined with monads, allowing us to do parallel processing with far less code and safer guarantees.
