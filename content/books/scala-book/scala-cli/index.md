---
title: "Using scala-cli"
summary: "Running the Scala REPL as well as programs in script files."
date: 2023-04-29T19:14:23-04:00
draft: true
authors:
    - "mattmoore"
series: ["Scala Book"]
series_order: 4
---

## What is scala-cli?

<mark>scala-cli</mark> is a newer method to quickly get started writing Scala code. It's not a full-fledged build tool, but allows us to easily write Scala programs and fire them up to run, with minimal configuration. It also contains a built-in <abbr title="A read–eval–print loop (REPL), also termed an interactive toplevel or language shell, is a simple interactive computer programming environment that takes single user inputs, executes them, and returns the result to the user; a program written in a REPL environment is executed piecewise. The term usually refers to programming interfaces similar to the classic Lisp machine interactive environment. Common examples include command-line shells and similar environments for programming languages, and the technique is very characteristic of scripting languages.">REPL</abbr> that makes it easy to experiment with Scala without needing to save code in a file.