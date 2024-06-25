---
title: "Scala Parallel Processing"
date: 2024-05-17T22:00:24-04:00
draft: true
---

There are many ways to do parallel processing in Scala. We'll take a look at a few of them in this article.

## cats-effect

### Supervisor

```scala
//> using scala "3.4.2"
//> using dep "org.typelevel::cats-core:2.10.0"
//> using dep "org.typelevel::cats-effect:3.5.4"

import cats.effect.{ExitCode, IO, IOApp, Temporal}
import cats.effect.std.Supervisor

import scala.concurrent.duration.*

type F[A] = IO[A]

def launch1(supervisor: Supervisor[F]): F[Unit] =
  supervisor.supervise(
    Temporal[F].sleep(10.seconds) >>
      IO.println("Supervisor code with embedded supervisor 1")
  ).void

def launch2(supervisor: Supervisor[F]): F[Unit] =
  supervisor.supervise(
    Temporal[F].sleep(5.seconds) >>
      IO.println("Supervisor code with embedded supervisor 2")
  ).void

object SupervisorTest extends IOApp {
  override def run(args: List[String]): IO[ExitCode] =
    Supervisor[F](await = true).use { supervisor =>
      for {
        _ <- launch1(supervisor)
        _ <- launch2(supervisor)
      } yield ExitCode.Success
    }
}
```
