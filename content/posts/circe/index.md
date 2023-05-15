---
title: "Circe"
summary: "Circe can be difficult to figure out - which imports to use, auto vs semiauto derivation, etc..."
date: 2023-05-03T00:27:57-04:00
draft: false
---

Circe can be difficult to figure out - which imports to use, auto vs semiauto derivation, etc...

## Circe Standalone

### Semiauto Derivation

```scala
//> using scala "3.2.2"
//> using dep "io.circe::circe-core:0.14.5"
//> using dep "io.circe::circe-generic:0.14.5"
//> using dep "io.circe::circe-parser:0.14.5"

import io.circe.*
import io.circe.syntax.*
import io.circe.generic.semiauto.*

case class User(id: Int, firstName: String, lastName: String)

// Just the encoder
given Encoder[User] = deriveEncoder
// Just the decoder
given Decoder[User] = deriveDecoder
// Codec derives both encoder and decoder
// given Codec[User] = deriveCodec

@main
def main() =
  // Successful conversion to JSON
  println {
    User(1, "Matt", "Moore").asJson
  }

  // Successful JSON parse
  println {
    val json =
      """{
            "id": 1,
            "firstName": "Matt",
            "lastName": "Moore"
          }"""
    parser.decode[User](json)
  }

  // Invalid JSON for the User type
  println {
    val json = """{"greeting": "Hello, Matt"}"""
    parser.decode[User](json)
  }
```

### Auto Derivation

```scala
//> using scala "3.2.2"
//> using dep "io.circe::circe-core:0.14.5"
//> using dep "io.circe::circe-generic:0.14.5"
//> using dep "io.circe::circe-parser:0.14.5"

import io.circe.*
import io.circe.syntax.*
import io.circe.generic.auto.*

case class User(id: Int, firstName: String, lastName: String)

@main
def main() =
  // Successful conversion to JSON
  println {
    User(1, "Matt", "Moore").asJson
  }

  // Successful JSON parse
  println {
    val json =
      """{
            "id": 1,
            "firstName": "Matt",
            "lastName": "Moore"
          }"""
    parser.decode[User](json)
  }

  // Invalid JSON for the User type
  println {
    val json = """{"greeting": "Hello, Matt"}"""
    parser.decode[User](json)
  }
```

## With http4s / cats-effect

### Semiauto Derivation

```scala
//> using scala "3.2.2"

//> using dep "io.circe::circe-core:0.14.5"
//> using dep "io.circe::circe-generic:0.14.5"
//> using dep "io.circe::circe-parser:0.14.5"

//> using dep "org.http4s::http4s-circe:0.23.18"
//> using dep "org.http4s::http4s-dsl:0.23.18"
//> using dep "org.http4s::http4s-ember-client:0.23.18"
//> using dep "org.http4s::http4s-ember-server:0.23.18"

//> using dep "org.typelevel::cats-effect:3.4.10"

//> using dep "ch.qos.logback:logback-classic:1.4.7"

import cats.effect.*

import com.comcast.ip4s.*

import io.circe.*
import io.circe.syntax.*
import io.circe.generic.semiauto.*

import org.http4s.*
import org.http4s.circe.*
import org.http4s.dsl.io.*
import org.http4s.implicits.*
import org.http4s.ember.server.*

case class HelloResponse(greeting: String)

given Encoder[HelloResponse] = deriveEncoder
given Decoder[HelloResponse] = deriveDecoder
// Both decoder and encoder as a codec
// given Codec[HelloResponse] = deriveCodec[HelloResponse]

object Main extends IOApp {
  val helloWorldService = HttpRoutes.of[IO] {
    case GET -> Root / "hello" / name =>
      Ok(HelloResponse(s"Hello, $name").asJson)
  }.orNotFound

  def run(args: List[String]): IO[ExitCode] =
    EmberServerBuilder
      .default[IO]
      .withHost(ipv4"0.0.0.0")
      .withPort(port"8080")
      .withHttpApp(helloWorldService)
      .build
      .use(_ => IO.never)
      .as(ExitCode.Success)
}
```

### Auto Derivation

```scala
//> using scala "3.2.2"

//> using dep "io.circe::circe-core:0.14.5"
//> using dep "io.circe::circe-generic:0.14.5"
//> using dep "io.circe::circe-parser:0.14.5"

//> using dep "org.http4s::http4s-circe:0.23.18"
//> using dep "org.http4s::http4s-dsl:0.23.18"
//> using dep "org.http4s::http4s-ember-client:0.23.18"
//> using dep "org.http4s::http4s-ember-server:0.23.18"

//> using dep "org.typelevel::cats-effect:3.4.10"

//> using dep "ch.qos.logback:logback-classic:1.4.7"

import cats.effect.*

import com.comcast.ip4s.*

import io.circe.*
import io.circe.syntax.*
import io.circe.generic.auto.*

import org.http4s.*
import org.http4s.circe.*
import org.http4s.dsl.io.*
import org.http4s.implicits.*
import org.http4s.ember.server.*

case class HelloResponse(greeting: String)

object Main extends IOApp {
  val helloWorldService = HttpRoutes.of[IO] {
    case GET -> Root / "hello" / name =>
      Ok(HelloResponse(s"Hello, $name").asJson)
  }.orNotFound

  def run(args: List[String]): IO[ExitCode] =
    EmberServerBuilder
      .default[IO]
      .withHost(ipv4"0.0.0.0")
      .withPort(port"8080")
      .withHttpApp(helloWorldService)
      .build
      .use(_ => IO.never)
      .as(ExitCode.Success)
}
```