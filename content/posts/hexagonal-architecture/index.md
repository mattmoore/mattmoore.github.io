---
title: "Hexagonal Architecture"
date: 2023-05-13T10:03:17-04:00
draft: true
---

You may have heard of hexagonal architecture, but wondered what it is. It's a design pattern that focuses on separating logic from implementation. But what does that mean?

Let's say we're writing some software that's going to talk to a database. We need to store records, retrieve them, update them and sometimes delete them. These operations are commonly called CRUD (Create, Read, Update, Delete).

Oftentimes, software ends up with the details of *how* to do CRUD operations mixed in with the flow of our program. Then, when we or others try reading our code, it's often difficult to see the full logic of our program at a glance.

With hexagonal architecture, we define the overall *what* our program is doing and separate the *how* our program does that. The *what* is defined in *ports* and the *how* is defined in *adapters*.

## An Example Without Hexagonal Architecture

Let's start with an example. If we have a user service that can CRUD users, we might build a `UserRepository` and a `UserService` like this:

{{< mermaid >}}
classDiagram
    class User {
        +UUID id
        +String name
    }

    class UserRepository {
        +Connection connection
        +UUID create(User)
        +User read(UUID)
        +Int update(User)
        +Int delete(UUID)
    }

    class UserService {
        +UserRepository repo
        +UUID create(User)
        +User read(UUID)
        +Int update(User)
        +Int delete(UUID)
    }

    UserService ..> User
    UserRepository ..> User
    UserService ..> UserRepository
{{< /mermaid >}}

If we are going to create a user, we would call the `UserService.create(User)`. Writing this in Scala might look like this:

{{< scastie "KEswr7FPQCK6l6MVcVIVHw/2" >}}

This looks great. It works, the service is calling a repository to do the saving, and we get the ID back from the database call, ensuring the user was saved.

But what if we first wrote this with PostgreSQL and later decided we wanted to support both PostgreSQL and DynamoDB? The current design doesn't allow us to do that. This is where hexagonal architecture comes in. Rather than defining a `UserService` and `UserRepository`, we can define these using ports and adapters.

## Using Hexagonal Architecture: Ports and Adapters

With hexagonal architecture, we would define ports, then implement the behavior in adapters. If we apply this principal to our user service, we would start in Scala with these traits:

- `core.ports.UserRepository`
- `core.ports.UserService`

And then we would define adapters to describe our implementations. One way to do this is by creating Scala classes that extend the "port" traits above:

- `core.adapters.UserRepository`
- `core.adapters.UserService`

This makes our original design now look something like this:

{{< mermaid >}}
classDiagram
    class `core.domain.User` {
        +UUID id
        +String name
    }

    class `core.ports.UserRepository` {
        << interface >>
        Connection connection
        UUID create(core.domain.User)
        core.domain.User read(UUID)
        Int update(core.domain.User)
        Int delete(UUID)
    }

    class `core.ports.UserService` {
        << interface >>
        core.ports.UserRepository repo
        UUID create(core.domain.User)
        core.domain.User read(UUID)
        Int update(core.domain.User)
        Int delete(UUID)
    }

    class `core.adapters.postgres.UserRepository` {
        Connection connection
        UUID create(core.domain.User)
        core.domain.User read(UUID)
        Int update(core.domain.User)
        Int delete(UUID)
    }

    class `core.adapters.UserService` {
        << service >>
        core.ports.UserRepository repo
        UUID create(core.domain.User)
        core.domain.User read(UUID)
        Int update(core.domain.User)
        Int delete(UUID)
    }

    `core.ports.UserRepository` <|.. `core.adapters.postgres.UserRepository` : implements
    `core.ports.UserService` <|.. `core.adapters.UserService` : implements

    `core.ports.UserRepository` ..> `core.domain.User`
    `core.ports.UserService` ..> `core.domain.User`
    `core.ports.UserService` ..> `core.ports.UserRepository`
{{< /mermaid >}}

Instead of classes to implement our trait functionality, we can also define initialization functions for our traits:

```scala
def userPostgresRepository = new UserRepository {...}

def userService(repo: UserRepository) = new UserService {...}
```

Putting all this together, we can write a program like this:

{{< scastie "8I3Yx0mAQymuGyV24lm4Og/26" >}}
