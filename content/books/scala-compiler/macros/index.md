---
title: "Macros"
summary: "With a macro, we can treat programs as values, which allows us to analyze and generate them at compile time."
date: 2023-05-25T19:08:12-04:00
draft: false
authors:
    - "mattmoore"
series: ["Scala Compiler"]
series_order: 1
tags:
    - "scala"
    - "scala-compiler"
---

## What are macros?

With a macro, we can treat programs as values, which allows us to analyze and generate them at compile time.

## A simple macro to print execution time

Let's start with a simple example. We'll create a function that times a given operation and prints the time it took to run the function.

### Macro Definition - `Macro.scala`

When creating macros, you need to make sure they exist in a separate file. We'll create a `Macro.scala` file and add this code to it:

```scala
import scala.quoted.*

object Macro:
  inline def timed[T](inline expr: T): T = ${ timedImpl('expr) }

  private def timedImpl[T: Type](expr: Expr[T])(using Quotes): Expr[T] =
    '{
      val start = System.currentTimeMillis()
      try $expr
      finally {
        val end = System.currentTimeMillis()
        val exprAsString = ${ Expr(expr.show) }
        println(s"Evaluating $exprAsString took: ${end - start}ms")
      }
    }

  private def exprAsCompactString[T: Type](expr: Expr[T])(using Quotes): String =
    import quotes.reflect.*
    expr.asTerm match {
      case Inlined(_, _, Apply(method, params)) => s"${method.symbol.name}(${params.map(_.show).mkString(",")})"
      case _                                    => expr.show
    }
```

### Macro Usage - `App.scala`

```scala
//> using scala "3.2.2"

import Macro.*

def f(n: Int) = {
  Thread.sleep(500L * n)
  20
}

@main
def main =
  timed {
    println("Start2")
    Thread.sleep(1000L)
    println("End")
  }

  timed(f(1))
  timed(f(2))
```

Run `scala-cli .`:

```shell
Start2
End
Evaluating {
  scala.Predef.println("Start2")
  java.lang.Thread.sleep(1000L)
  scala.Predef.println("End")
} took: 1076ms
Evaluating App$package.f(1) took: 503ms
Evaluating App$package.f(2) took: 1002ms
```

## Customizing the macro output

### Macro Definition - `Macro.scala`

```scala
import scala.quoted.*

object Macro:
  inline def timed[T](inline expr: T): T = ${ timedImpl('expr) }

  private def timedImpl[T: Type](expr: Expr[T])(using Quotes): Expr[T] =
    '{
      val start = System.currentTimeMillis()
      try $expr
      finally {
        val end = System.currentTimeMillis()
        val exprAsString = ${ Expr(exprAsCompactString(expr)) }.replaceAll("\\s+", " ").trim()
        val exprAsStringShort = if (exprAsString.length > 50) exprAsString.take(50) + "..." else exprAsString
        println(s"Evaluating $exprAsStringShort took: ${end - start}ms")
      }
    }

  private def exprAsCompactString[T: Type](expr: Expr[T])(using Quotes): String =
    import quotes.reflect.*
    expr.asTerm match {
      case Inlined(_, _, Apply(method, params)) => s"${method.symbol.name}(${params.map(_.show).mkString(",")})"
      case _                                    => expr.show
    }
```

### Macro Usage - `App.scala`

```scala
//> using scala "3.2.2"

import Macro.*

def f(n: Int) = {
  Thread.sleep(500L * n)
  20
}

@main
def main =
  timed {
    println("Start2")
    Thread.sleep(1000L)
    println("End")
  }

  timed(f(1))
  timed(f(2))
```

Run `scala-cli .`:

```shell
Start2
End
Evaluating { scala.Predef.println("Start2") java.lang.Thread.... took: 1069ms
Evaluating f(1) took: 504ms
Evaluating f(2) took: 1004ms
```
