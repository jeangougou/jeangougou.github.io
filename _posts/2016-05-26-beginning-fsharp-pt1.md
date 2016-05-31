---
layout: post
title:  "Beginning F# pt1"
date:   2016-05-26 16:04:23 +0000
categories: programming
description: "beginning F# development, sample scripts and introduction to functional programming. Loops, conditions and recursion"
published: true
tags:
- fsharp
- F#
- functional programming
---

We are about to explore some interesting capabilities of the F# environment.
Some code snippets built to show the capabilities of the language and the expressiveness of the syntax.

### What do you mean with it's a functional language ?

A little theoretical background never hurts. By [Functional programming language][wiki_Functional_programming] I mean a language that threats everything as a function. That's one of the _naive_ definitions you can find around, let's explain it better. Formally the definition is:

>  functional programming is a programming paradigm that treats computation as the evaluation of mathematical functions and avoids changing-state and mutable data.

That doesn't sound very intuitive, does it ?
In reality it is simpler than the definition might lead you to believe. It's closely related to the Math courses everyone has already taken at school. For instance, having a function defined as ```y = f(x)``` where ```f(x) = x + 2``` we know that any time we want to know the value of ```y``` we need to evaluate ```f(x)``` depending on the selected ```x```. It is obvious at this point that if ```x = 3``` follows ``` y = 5 ```, or if ```x = 5``` then ``` y = 7 ```. This shows that despite current status, the value of ```y``` depends only from the value of ```x``` (x being the input of the function in exam  ```f(x)```). This level of isolation is very beneficial, particularly when developing in teams.

### Which functional language is better ?

That's an interesting question and there is no correct answer to it in general. There are, though, good answers for specific cases. I started learning ```F#``` because it comes with different versions of the .NET framework, which one way or the other you will have installed under Windows, also previous experiences with Microsoft software development which helped in understanding the general direction of the language. But any functional language can be a viable option given the right conditions.

### How can I start

On windows you can find the F# 4.0 compiler in the path ```C:\Program Files\Microsoft SDKs\F#\4.0\Framework\v4.0\fsi.exe``` or ```C:\Program Files (x86)\Microsoft SDKs\F#\4.0\Framework\v4.0\fsi.exe``` if you are using a 32-bit operating system.
I'm assuming you have installed it already from [fsprojects][F#github] or you can follow instructions to install it on many different operating systems from the [F# Official website][F# Official website].

In this tutorial the code will be compatible with both the [REPL][REPL] or through compilation and debugging in visual studio or other IDEs.

The small trick used to make it work on both environments is the use of compiler directives that can separate the scenarios for us. The [REPL][REPL] will leverage ```INTERACTIVE```, while debug and run from the IDE will interpret ```COMPILED```. Snippet to handle the logic is:
```
open System

// Compiled only
#if COMPILED
[<EntryPoint>]
#endif
let main argv =
  0

// REPL only
#if INTERACTIVE
fsi.CommandLineArgs |> Seq.skip 1 |> Seq.toArray |> main
#endif

```

### Loops and conditional statements

In F#, like most programming languages, we have access to low level constructs as Loops and conditional statements
The following program will compare a set of numbers to the number 5 to assess if they are lower than, greater than or equal to. It will do so for every positive integer from 0 to the parameter specified when launching the program.

```
open System

let ignore _ = ()
let checknum v =
  if v = 5 then "I am 5"
  elif v < 5 then "I am less than 5"
  else "I am a number above 5"

#if COMPILED
[<EntryPoint>]
#endif
let main argv =
  printfn "Insert a number to assess its relation to 5:"
  let check = Console.ReadLine()
  let check = int check
  for i = 0 to check do
    printfn "%A" [string i;(checknum i)]
  Console.ReadKey() |> ignore
  0

#if INTERACTIVE
fsi.CommandLineArgs |> Seq.skip 1 |> Seq.toArray |> main
#endif
```

Let's explain what happens more in detail. The initial part of the program is defining two functions, building blocks of functional languages. The ```ignore``` function is a noop function, as in no-operation, and it is effectively an empty function, as it can be guessed by its body being composed only of a parenthesis expression. While the ```checknum``` function operates upon the parameter named ```v``` and returns a string describing its relation to the number 5.
The ```checknum``` function contains conditional statements ```if ... elif ... else``` to return different values operating on the validity of the condition. It is noticeable also the fact that the comparison operator is written with a single ```=``` sign rather than the 2 (or 3) more common in other languages. This is a change that improves readability leaving the source-code neater, easier to understand and more concise.

Following that the ```main``` function, structured, as we've explained at the beginning, to run both with REPL or IDEs, contains the main logic of the program. It asks initially for a number from console. The variable ```check``` will contain what has been inserted and it will cast it to ```int``` in the following line.

Mind that this behavior is fine only for type casting, or changing the type of a variable. If we wrote something like:

```
  let check = Console.ReadLine()
  let check = 8
```

The second line would have thrown an error as the variable check as to be marked as ```mutable``` to change its value.
After having read the number from console, the program proceeds with a for loop, ```for ... to ... do``` that, for each positive integer number from zero to the value inserted from console it will print the result of the ```checknum``` function.

To keep the console open and show the results we are using a trick. We wait for another key to be pressed through ```Console.ReadKey()``` and ignoring what happens afterwards and removing a compiler warning by appending ``` |> ignore``` to it.

The last line of the ```main``` function is a simple ```0```. This represents the default return for the function as any function is supposed to return a value. This helps if we decide to write shell scripts too as any other number would be considered an error and allows for piping its current results without changing the source-code.

### Recursion

An old professor of mine used to say:

> to understand recursion you need to understand recursion

As much as a circular explanation it sound, it's still pretty much a correct one. Intuitively a mathematical _Recursion_ can be defined as running a procedure (a procedure being a set of steps following a set of rules) where apart from some edge cases where the result is known, some steps in the procedure can be calculated only by calling the procedure itself.
The most famous example of a recursive function is generation of the [Fibonacci sequence][Fibonacci_Sequence], named after the Italian mathematician.

Here's a buggy implementation in F#:

```
open System

let rec fibonacci a =
  if a = 0 then
    2
  else
    fibonacci (a-1) * a

#if COMPILED
[<EntryPoint>]
#endif
let main argv =
  let a = Console.ReadLine();
  let a = int a
  let calc = fibonacci a
  Console.WriteLine(calc)
  Console.ReadKey()
  0

#if INTERACTIVE
fsi.CommandLineArgs |> Seq.skip 1 |> Seq.toArray |> main
#endif
```

Again, skipping all the compiler directives elements, we notice that there are 2 main blocks. The ```main``` program and the recursive ```fibonacci``` function.
The ```main``` function starts by expecting an integer number from the console command line and proceeds to evaluate the function ```fibonacci``` before printing the results.

The ```fibonacci``` function itself is defined with the special keyword ```rec``` which allows for [tail call optimization][tail call optimization]. This explicitly tells the compiler to handle it as a recursive function and apply all possible optimization to it.
The simple ```if...else``` statement breaks down the function into the known edge-case or the recursive self-call with the current and the previous index of the sequence.

Stay tuned for the next article


[tail call optimization]:https://en.wikipedia.org/wiki/Tail_call
[Fibonacci_Sequence]:https://en.wikipedia.org/wiki/Fibonacci_number
[REPL]:https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop
[F# Official website]:http://fsharp.org/
[F#github]:http://fsprojects.github.io/VisualFSharpPowerTools/
[wiki_Functional_programming]:https://en.wikipedia.org/wiki/Functional_programming
