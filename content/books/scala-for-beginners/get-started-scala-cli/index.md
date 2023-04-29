---
title: "Installing Scala"
date: 2023-04-28T21:53:16-04:00
draft: false
authors:
    - "mattmoore"
series: ["Scala for Beginners"]
series_order: 2
---

## Introduction

Traditionally Scala projects have been built using <mark>sbt</mark>. There is another build tool on the block called <mark>mill</mark> which will be reserved for another post.

One of the complaints about Scala, especially among newcomers, is difficulty with rapidly firing up Scala files or scripts to be able to test or experiment with the language. To improve upon this, there is a new tool available called <mark>scala-cli</mark>.

Throughout this book, we will be using <mark>scala-cli</mark> to keep things simple and focus on the language concepts.

## scala-cli

<mark>scala-cli</mark> is a newer method to quickly get started writing Scala code. It's not a full-fledged build tool, but allows us to easily write Scala programs and fire them up to run, with minimal configuration. It also contains a built-in <abbr title="A read–eval–print loop (REPL), also termed an interactive toplevel or language shell, is a simple interactive computer programming environment that takes single user inputs, executes them, and returns the result to the user; a program written in a REPL environment is executed piecewise. The term usually refers to programming interfaces similar to the classic Lisp machine interactive environment. Common examples include command-line shells and similar environments for programming languages, and the technique is very characteristic of scripting languages.">REPL</abbr> that makes it easy to experiment with Scala without needing to save code in a file.

### Install scala-cli

For full information on getting Scala set up on a variety of platforms, please see the [Scala Getting Started Guide](https://docs.scala-lang.org/getting-started/index.html).

For users on macOS with [homebrew](https://brew.sh), you can install Scala with this command:

```shell
brew install coursier/formulas/coursier && cs setup
```
