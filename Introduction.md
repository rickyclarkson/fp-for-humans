Functional Programming for Humans

Coming from an imperative background with a reasonable background in mathematics I found it tricky to break into the world of functional programming although that's what my code was telling me to aim at.  This guide attempts to explain various concepts to my former self, and I hope that you find it useful.  Let's go.

Functions: What and Why

A function is some item that takes arguments and gives a result.  It doesn't have to be executed to have some meaning though generally that's what we'll do when programming.  For instance, you can tell me that sin(3) - sin(3) is 0 without running anything; you can *reason* about the expression.

A function isn't something that you execute to make something happen; e.g., writing to a file is not a function.  Some languages have exceptions, which let you jump to a point in calling code with some context.  Anything that throws an exception is not a function at least if it's given parameters that cause an exception.

Functions can take other functions as parameters and return them as results, and without any other data types around functions are enough to compute anything we care to, though without datatypes that could be pretty cumbersome.

As an example, booleans exist in most programming languages; essentially a value that may be true or false.  With just functions we could say that true is a function that takes 2 parameters and returns the first, and false is a function that takes 2 parameters and returns the second, which is enough to give us everything an if statement gives us in a programming language.  There are encodings for numbers too.

Data

Most programming languages give a way of defining datatypes; effectively names for certain types of data.  E.g., the numbers encoding in pure functions has true being the exact same function as 0, and those are two concepts you don't really want to be able to mix accidentally.  To use a slightly more realistic example, if I'm continually dealing with colour values in both ARGB and AHSV I don't want to directly use a standard 32-bit integer because that gives me no way to detect, say, passing a AHSV value to a function expecting a ARGB value.

data ARGB = ARGB Byte Byte Byte Byte

data AHSV = AHSV Byte Byte Byte Byte

That separates them.  Most languages will have some equivalent to that, and may encourage or force you to name each of the 4 parts.

Now I can write a function that expects a ARGB and be told when I try to call it with an AHSV as a parameter.  When you actually get that error depends on whether the data type is checked without running the program or during it based on the actual values flying around, covered more in [Types].

Types

There are various meanings for the word type, probably because it's quite a convenient word to use.  Some languages claim to check their types at runtime, which makes them dynamically typed.  However, for the purposes of this guide we'll generally say that those languages are untyped.  That means that each expression or variable that you see in the code does not have a type assigned, and therefore type errors are not discovered before running the program (and running it in such a way that the error is found).

For instance, x = "hello" - 3 might be a line of code the runtime rejects, but if you hide it as x = if (year < 2015) "early" else "hello" - 3, you won't get an error until year reaches 2015 or greater.

These are boiled-down, minimal examples of things that can and do happen in projects of all sizes and covering the code with copious amounts of tests will genuinely find a lot of these problems but not all.  Types, as in expression and variable or 'static' types, can find all of these problems.

There is some argument that novice programmers will get more out of an untyped language, or a typed language that doesn't prevent all errors, because there might be less code needed for certain tasks, or it might be easier to get something running (even if it's not really going to work).  There are even systems like Erlang, designed to be able to continue and recover, possibly shutting down subsystems, on failure, and many languages give more understandable runtime errors than compile-time errors.

Typed languages that prevent all errors are called 'dependently-typed' languages, and are generally also proof assistants, i.e., they help you to prove a mathematical fact.

An interesting concept there is the 'witness', a function parameter that by its very presence means that we know something extra about some data just by the parameter being there.

For example, if we have two parameters, a and b, and a third, 'GreaterThan a b' then we know a > b and so does the type system.  It's practically a little less magical than that but we can use witnesses to 'carry proofs', letting us build code knowing that certain preconditions are met because otherwise the code will not even compile.

In untyped or typed but not dependently-typed languages we can do the same but carry the proofs in our heads.  If I test a parameter for equality to null and it gives false, I carry in my head the fact that the parameter is not null.  The less typing you have or use the more you need to carry in your head and the less you can have the machine check.

Lenses

A problem people often have moving from the imperative world is that of updating one or two values in a record, especially when that record is nested in another.  We can't update the values in-place because that would make f(x) able to give a different result each time it's called, and we don't want to have to copy the entire record on every update.

One way of doing it


