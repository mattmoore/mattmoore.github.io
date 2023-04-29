---
title: "Installing Scala"
summary: "How to get Scala installed and running locally."
date: 2023-04-29T19:14:10-04:00
draft: false
series: ["Scala for Beginners"]
series_order: 2
---

## Common build tools for Scala

### sbt

https://www.scala-sbt.org

Traditionally Scala projects have been built using <mark>sbt</mark>. Despite it's high usage in the Scala community, many people claim to dislike it. Personally, I've rather enjoyed working with <mark>sbt</mark> and have generally found it easier to use than the typical <mark>maven</mark> or even <mark>gradle</mark> build tools commonly used with JVM projects.

### mill

https://com-lihaoyi.github.io/mill/mill/Intro_to_Mill.html

<mark>mill</mark> is another build tool - kind of the "new kid on the block".

### scala-cli

https://scala-cli.virtuslab.org

<mark>scala-cli</mark> comes with the standard Coursier distribution of Scala. It's not a full-fledged build tool, but provides a built-in <abbr title="A read–eval–print loop (REPL), also termed an interactive toplevel or language shell, is a simple interactive computer programming environment that takes single user inputs, executes them, and returns the result to the user; a program written in a REPL environment is executed piecewise. The term usually refers to programming interfaces similar to the classic Lisp machine interactive environment. Common examples include command-line shells and similar environments for programming languages, and the technique is very characteristic of scripting languages.">REPL</abbr> and allows for easily pulling in dependencies (external libraries, etc...). Throughout this book, we will be using the official Coursier distribution of Scala and will also be using <mark>scala-cli</mark> to keep things simple and focus on the language concepts through most of this book, rather than on configuring build tools. There will be separate chapters at the end for the other build tools.

## Install Scala with Coursier

For full information on getting Scala set up on a variety of platforms, please see the [Scala Getting Started Guide](https://docs.scala-lang.org/getting-started/index.html).

For users on macOS with [homebrew](https://brew.sh), you can install Scala with this command:

```shell
brew install coursier/formulas/coursier && cs setup
```
