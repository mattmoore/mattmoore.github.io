---
title: "Introduction"
summary: "Create the compiler plugin entrypoint."
date: 2020-04-21T09:34:30-04:00
draft: true
authors:
    - "mattmoore"
series: ["Scala Compiler"]
series_order: 1
tags:
    - "scala"
    - "scala-compiler"
# Original URL: https://mattmoore.io/blog/writing-scala-compiler-plugins
---

## Compiler Phases

If you’re reading this you probably know what a compiler is. It takes your source code and converts it to a program that can run on your machine. In a nutshell, the compiler converts your source code into tokens (this is called lexing), then converts those tokens into a tree (called parsing), then it goes through multiple steps, depending on the compiler in question. For example, with Scala, there would be steps to do type-checking and a whole host of other things. Finally, the compiler goes through a process called code generation that creates the binary program. Each step in the compiler is called a “phase”.

In Scala, you can actually see what these phases are. If you have the Scala compiler installed, you can run this command in your terminal to see them:

```shell
scalac -Xshow-phases
```

{{<detail-tag "View compiler phases">}}
```
            phase name  description
            ----------  -----------
                parser  scan and parse sources
                 typer  type the trees
      inlinedPositions  check inlined positions
              sbt-deps  sends information on classes' dependencies to sbt
     extractSemanticDB  extract info into .semanticdb files
             posttyper  additional checks and cleanups after type checking
         prepjsinterop  additional checks and transformations for Scala.js
               sbt-api  sends a representation of the API of classes to sbt
           SetRootTree  set the rootTreeOrProvider on class symbols
               pickler  generates TASTy info
              inlining  inline and execute macros
          postInlining  add mirror support for inlined code
               staging  check staging levels and heal staged types
              splicing  splicing
          pickleQuotes  turn quoted trees into explicit run-time data structures
    instrumentCoverage  instrument code for coverage checking
        firstTransform  some transformations to put trees into a canonical form
        checkReentrant  check no data races involving global vars
   elimPackagePrefixes  eliminate references to package prefixes in Select nodes
          cookComments  cook the comments: expand variables, doc, etc.
           checkStatic  check restrictions that apply to @static members
 checkLoopingImplicits  check that implicit defs do not call themselves in an
                        infinite loop
            betaReduce  reduce closure applications
            inlineVals  check right hand-sides of an `inline val`s
            expandSAMs  expand SAM closures to anonymous classes
          elimRepeated  rewrite vararg parameters and arguments
             refchecks  checks related to abstract members and overriding
           initChecker  check initialization of objects
    crossVersionChecks  check issues related to deprecated and experimental
    protectedAccessors  add accessors for protected members
            extmethods  expand methods of value classes with extension methods
   uncacheGivenAliases  avoid caching RHS of simple parameterless given aliases
            elimByName  map by-name parameters to functions
        hoistSuperArgs  hoist complex arguments of supercalls to enclosing scope
      forwardDepChecks  ensure no forward references to local vals
specializeApplyMethods  adds specialized methods to FunctionN
      tryCatchPatterns  compile cases in try/catch
        patternMatcher  compile pattern matches
            preRecheck  preRecheck
               recheck  recheck
            preRecheck  preRecheck
                    cc  cc
            elimOpaque  turn opaque into normal aliases
     explicitJSClasses  make all JS classes explicit
         explicitOuter  add accessors to outer classes from nested ones
          explicitSelf  make references to non-trivial self types explicit as
                        casts
         interpolators  optimize s, f, and raw string interpolators
       pruneErasedDefs  drop erased definitions and simplify erased expressions
         uninitialized  eliminates `compiletime.uninitialized`
        inlinePatterns  remove placeholders of inlined patterns
       vcInlineMethods  inlines calls to value class methods
           seqLiterals  express vararg arguments as arrays
           intercepted  rewrite universal `!=`, `##` methods
               getters  replace non-private vals and vars with getter defs
   specializeFunctions  specialize Function{0,1,2} by replacing super with
                        specialized super
      specializeTuples  replaces tuple construction and selection trees
               liftTry  lift any try that might be executed on a non-empty
                        expression stack
 collectNullableFields  collect fields that can be nulled out after use in lazy
                        initialization
       elimOuterSelect  expand outer selections
          resolveSuper  implement super accessors
 functionXXLForwarders  add forwarders for FunctionXXL apply methods
       paramForwarding  add forwarders for aliases of superclass parameters
         genericTuples  optimize generic operations on tuples
          letOverApply  lift blocks from receivers of applications
     arrayConstructors  intercept creation of (non-generic) arrays and
                        intrinsify
               erasure  rewrite types to JVM model
   elimErasedValueType  expand erased value types to their underlying
                        implmementation types
             pureStats  remove pure statements in blocks
    vcElideAllocations  peep-hole optimization to eliminate unnecessary value
                        class allocations
             etaReduce  reduce eta expansions of pure paths
            arrayApply  optimize `scala.Array.apply`
    addLocalJSFakeNews  adds fake new invocations to local JS classes in calls
                        to `createLocalJSClass`
      elimPolyFunction  rewrite PolyFunction subclasses to FunctionN subclasses
               tailrec  rewrite tail recursion to loops
     completeJavaEnums  fill in constructors for Java enums
                 mixin  expand trait fields and trait initializers
              lazyVals  expand lazy vals
               memoize  add private fields to getters and setters
       nonLocalReturns  expand non-local returns
          capturedVars  represent vars captured by closures as heap objects
          constructors  collect initialization code in primary constructors
       instrumentation  count calls and allocations under -Yinstrument
            lambdaLift  lifts out nested functions to class scope
        elimStaticThis  replace This references to static objects by global
                        identifiers
    countOuterAccesses  identify outer accessors that can be dropped
    dropOuterAccessors  drop unused outer accessors
      checkNoSuperThis  check that supercalls don't contain references to This
               flatten  lift all inner classes to package scope
    transformWildcards  replace wildcards with default values
            moveStatic  move static methods from companion to the class itself
         expandPrivate  widen private definitions accessed from nested classes
         restoreScopes  repair rendered invalid scopes
          selectStatic  get rid of selects that would be compiled into GetStatic
    junitBootstrappers  generate JUnit-specific bootstrapper classes for
                        Scala.js
  Collect entry points  collect all entry points and save them in the context
     collectSuperCalls  find classes that are called with super
 repeatableAnnotations  aggregate repeatable annotations
              genSJSIR  generate .sjsir files for Scala.js
              genBCode  generate JVM bytecode
```
{{</detail-tag>}}

You can create your own phases and add them to the compiler after any of those phases. The information from a given phase then lets you modify the results of that phase before the compiler moves to the next phase. This allows you to modify the behavior of the compiler itself.

## Why Create a Plugin?

Suppose you wanted to add some functionality to Scala itself that doesn’t currently exist. Compiler plugins allow us to do just this. For the purposes of this article, I’m going to demonstrate how you might create a plugin to catch division by zero.

Let’s write a program in Scala to divide by zero:

```scala
object Test {
  val five = 5
  val amount = five / 0
  def main(args: Array[String]): Unit = {
    println(amount)
  }
}
```

If you compile this with scalac Test.scala and run it with scala Test, the compilation will succeed but running it will result in a Java arithmetic error. What if we can detect division by zero when compiling, and halt compilation? This would enable us to prevent releasing this code to production by accident. Let’s take a look at how this process works.

## Create an SBT Project

First, create a new sbt project:

```
sbt new scala/scala-seed.g8
```
You can fill in the details however you’d like. Take a look at the example plugin project I’ve created here. You can always use this as a starting point.

## Important Details for Compiling as a Plugin

Key components you’ll need in your plugin project:

### build.sbt

{{< figure src="build.sbt.png" caption="You can adjust these details however you’d like. Note the name entry, however. This will need to match the name in the next file below." >}}

```sbt
name := "division-by-zero"
version := "0.0.1-SNAPSHOT"
scalaVersion := "2.13.5"
organization := "io.mattmoore.scala2.compiler.plugins"
libraryDependencies += "org.scala-lang" % "scala-compiler" % "2.13.5"
sbtPlugin := false
```

### resources/scalac-plugin.xml

{{< figure src="scalac-plugin.xml.png" caption="This file contains information used to generate the META-INF data for the plugin. Ensure the name and classname tags match correctly with the information in the build.sbt file above." >}}

```xml
<plugin>
  <name>division-by-zero</name>
  <classname>io.mattmoore.scala2.compiler.plugins.DivisionByZero</classname>
</plugin>
```

### DivisionByZero.scala

{{< figure src="DivisionByZero.scala.png" caption="This is the actual compiler plugin. Note the package and name. These need to match the entries. in the scalar-plugin.xml file above, which in turn needs to match the build.sbt entries." >}}

```scala
package io.mattmoore.scala2.compiler.plugins

import scala.tools.nsc
import nsc.Global
import nsc.Phase
import nsc.plugins.Plugin
import nsc.plugins.PluginComponent

class DivisionByZero(val global: Global) extends Plugin {

  import global._

  val name = "division-by-zero"
  val description = "checks for division by zero"
  val components = List[PluginComponent](Component)

  private object Component extends PluginComponent {
    val global: DivisionByZero.this.global.type = DivisionByZero.this.global
    val runsAfter = List[String]("refchecks")
    val phaseName = DivisionByZero.this.name

    def newPhase(_prev: Phase) = new DivByZeroPhase(_prev)

    class DivByZeroPhase(prev: Phase) extends StdPhase(prev) {
      override def name = DivisionByZero.this.name

      def apply(unit: CompilationUnit): Unit = {
        for (tree@Apply(Select(rcvr, nme.DIV), List(Literal(Constant(0)))) <- unit.body
             if rcvr.tpe <:< definitions.IntClass.tpe) {
          global.reporter.error(tree.pos, "attempting division by zero")
        }
      }
    }
  }
}
```

There’s a lot going on in this file. We’ll examine it piece by piece. We’ve created an object called Component that extends PluginComponent, which is the underlying Scala class that represents a plugin.

```scala
private object Component extends PluginComponent
```

Then we specify the phase it should run after. Remember the phases we listed earlier with the scalac -Xshow-phases command we ran earlier? For this plugin, we are running it after the refchecks phase. It’s out of the scope of this article to explain why. I’ll have to write up another post explaining the individual phases later. For now, just humor me.

```scala
val global: DivisionByZero.this.global.type = DivisionByZero.this.global
val runsAfter = List[String]("refchecks")
val phaseName = DivisionByZero.this.name

def newPhase(_prev: Phase) = new DivByZeroPhase(_prev)
```

Next we’re defining the function DivByZeroPhase that we’re going to run during the new phase we’ve created. This is where the magic happens. We check to see if each node in the tree contains a division by zero.

We’re defining the apply method here which takes the CompilationUnit (the node in the tree) and does the check for division by zero. The nme.DIV is the division type.

Finally we call global.reporter.error(tree.pos, "attempting division by zero") which will halt the Scala compiler and display the error message.

```scala
class DivByZeroPhase(prev: Phase) extends StdPhase(prev) {
  override def name = DivisionByZero.this.name

  def apply(unit: CompilationUnit): Unit = {
    for (tree@Apply(Select(rcvr, nme.DIV), List(Literal(Constant(0)))) <- unit.body
      if rcvr.tpe <:< definitions.IntClass.tpe) {
        global.reporter.error(tree.pos, "attempting division by zero")
      }
    }
}
```

Now you’re ready to compile the compiler plugin for division by zero. Run this command to compile it, generate a jar file and publish the plugin to your local Ivy repository:

```shell
sbt compile package publishLocal
```

## Add the Plugin to Your Project

Now that we’ve got our plugin compiled and published to the local Ivy repository.

### build.sbt

The resolvers section tells the project to load the plugin from the Ivy repository on your local machine.

```scala
autoCompilerPlugins := true
addCompilerPlugin("io.mattmoore.scala2.compiler.plugins" %% "division-by-zero" % "0.0.1-SNAPSHOT")
resolvers += Resolver.mavenLocal
```

That’s it! now if you run sbt compile the Scala compiler should run the division by zero plugin against your project. If it finds any divisions by zero, it will error out and not continue with compilation. You can disable the plugin by commenting (or removing) the addCompilerPlugin line.

Lets compile the project without the plugin. Note that it will compile successfully but fail when we run it.

```shell
sbt compile
[info] Loading global plugins from /Users/mattmoore/.sbt/1.0/plugins
[info] Loading project definition from /Users/mattmoore/source/scala-compiler-plugin-example/use-plugins/division-by-zero/project
[info] Loading settings for project root from build.sbt ...
[info] Set current project to Using the division-by-zero plugin. (in build file:/Users/mattmoore/source/scala-compiler-plugin-example/use-plugins/division-by-zero/)
[info] Executing in batch mode. For better performance use sbt's shell
[info] Compiling 1 Scala source to /Users/mattmoore/source/scala-compiler-plugin-example/use-plugins/division-by-zero/target/scala-2.13/classes ...
[success] Total time: 3 s, completed Apr 21, 2020 7:20:44 PM
```

Let’s try running the program. You’ll notice it fails when running because division by zero (as most programmers and mathematicians know) is undefined.

```shell
sbt run
[error] Caused by: java.lang.ArithmeticException: / by zero
[error] 	at Test$.(Test.scala:3)
[error] 	at Test.main(Test.scala)
[error] 	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
[error] 	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
[error] 	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
[error] 	at java.lang.reflect.Method.invoke(Method.java:498)
```

Now let’s compile the project with the plugin enabled. Note that it fails during compilation with the error message we added in the plugin.

```shell
sbt compile
[info] Loading global plugins from /Users/mattmoore/.sbt/1.0/plugins
[info] Loading project definition from /Users/mattmoore/source/scala-compiler-plugin-example/use-plugins/division-by-zero/project
[info] Loading settings for project root from build.sbt ...
[info] Set current project to Using the division-by-zero plugin. (in build file:/Users/mattmoore/source/scala-compiler-plugin-example/use-plugins/division-by-zero/)
[info] Executing in batch mode. For better performance use sbt's shell
[info] Compiling 1 Scala source to /Users/mattmoore/source/scala-compiler-plugin-example/use-plugins/division-by-zero/target/scala-2.13/classes ...
[error] /Users/mattmoore/source/scala-compiler-plugin-example/use-plugins/division-by-zero/src/main/scala/io/mattmoore/Test.scala:3:21: attempting division by zero
[error]   val amount = five / 0
[error]                     ^
[error] one error found
[error] (Compile / compileIncremental) Compilation failed
[error] Total time: 2 s, completed Apr 21, 2020 7:19:39 PM
```

And that’s it! You’ve written your first Scala compiler plugin and run it in a project. But that’s not all compiler plugins are capable of. In the next Scala compiler post we’ll look at how to transform code, which allows us to add entirely new semantics to Scala.