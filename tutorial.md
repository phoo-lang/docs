# Phoo Tutorial

This page serves as a quick introduction to the programming language Phoo and how to write code in it. While Phoo is definitely new, it is a full-featured language, and once the hurdle of reverse Polish notation is overcome, Phoo code is quick and rewarding.

*This tutorial is adapted in part from the tutorial in* [The Book of Quackery](https://github.com/GordonCharlton/Quackery/blob/main/The%20Book%20of%20Quackery.pdf), *by Gordon Charlton, due to the fact that Phoo is adapted in part from Quackery.*

[TOC]
<!-- markdownlint-disable MD014 MD040 -->

## Getting Started

Phoo is not yet distributed as an executable file. However, a simple online shell has been jimmied up at <https://phoo-lang.github.io/>, so head over there to try it out. You should get something like this:

```
Welcome to Phoo.
Version 0.2.0 (e03dead)
Shell at 783f999
Strict mode is ON
Press Shift+Enter for multiple lines.
(0)--> 
```

At the prompt, you type your code, and the Phoo machine evaluates it and puts the results on the stack.

Alternatively, for the code here, you can hover over the box and click "Run this code" at the top right, which will launch the aforementioned online shell and trigger it to run that code as if you pasted it into the first prompt.

## Phoo Says Hello World

The obligatory first task for any programming language is to print "Hello, World!". The code to accomplish that in Phoo is this:

```phoo
$ "Hello, World!" echo
```

Now how did Phoo do that? The answer is not so simple, and hopefully by the end of this tutorial, you should be able to answer that question.

The next usual task for a programming language is basic arithmetic. So, we ask Phoo, what is 2+2?

```phoo
2+2
```

Try it out. Back? Good. Got an error? Even better. The first rule of Phoo is that separate words (such as "2" and "+") must have whitespace between. So we try again:

```phoo
2 + 2
```

Did you still get an error? That is to be expected as well. Phoo is categorized as a concatenative programming language, which means that the operands must come before the operation. This is also known as [reverse Polish notation](https://en.wikipedia.org/wiki/Reverse_Polish_notation), which utilizes a stack. Each operand is pushed to the stack, and operators pop the operands back off the stack and push the result. So we must rearrange:

```phoo
2 2 +
```

Hooray! It worked. The twos were pushed onto the stack and `:::phoo +` added them, leaving the answer, 4, on the stack.

For a more concrete example on how the stack works, the usual in-fix expression `(3*4)+(5*6)` translates in reverse-Polish notation to `3 4 + 5 6 + *`. I have inserted `:::phoo stacksize pack dup dip unpack echostack` (which prints out the stack without altering it) after every word in the expression below, to visualize what is going on to and off of the stack:

```phoo
3 stacksize pack dup dip unpack echostack
4 stacksize pack dup dip unpack echostack
* stacksize pack dup dip unpack echostack
5 stacksize pack dup dip unpack echostack
6 stacksize pack dup dip unpack echostack
* stacksize pack dup dip unpack echostack
+
```

Here's what happens, line by line:

1. 3 is pushed to the stack.
2. 4 is pushed to the stack.
3. `:::phoo *` multiplies the two and leaves 12 on the stack.
4. 5 is pushed to the stack.
5. 6 is pushed to the stack.
6. `:::phoo *` multiples the 5 and 6, leaving 30, and the 12 is not touched.
7. `:::phoo +` adds the 12 and 30, resulting in 42 for the entire expression.

While this may seem like a lousy way to put 42 on the stack, not every input in an expression is going to be hard-coded. In most every other case, it's not going to be 3, it's going to be some data taken from elsewhere that needs to be processed. A thourough understanding of stack-based post-fix arithmetic is essential to programming in Phoo.

At this point we have now seen examples of words. Words are simply any sequence of non-whitespace characters that Phoo recognizes --- such as `:::phoo echo` and `:::phoo +`. To see a list of all the words available to you, type `:::phoo dir` at the prompt and it will put a long list of strings on the stack.

We have also seen examples of literals. A literal is special type of word that simply represents a primitive value, such as `:::phoo 5`. Literals are described by a regular expression, and the builtin library has several regular expressions for different number formats, among others.

Lastly, we have seen an example of a macro. The macro `:::phoo $` handles the compilation of strings. Macros are not limited to one word's worth as are literals -- they can use the entirety of the source string after them, and modify the code behind them. I won't be getting into macros in too much depth here, but the [Internals document](internals.html#compilation) has some good explanations of how macros and literals are recognized and interpreted by the Phoo compiler.

## Sneaky Sneaky Sub-Arrays

The final construct (not seen so far) is simply a sub-array. These are created using the macros `:::phoo do`/`:::phoo [` and `:::phoo end`/`:::phoo ]`. Sub-arrays are the core of program stucture, and have a few caveats.

!!! note "Side note on code style"
    `:::phoo do` and `:::phoo [` (and likewise `:::phoo end` and `:::phoo ]`) are interchangeable and can be chosen freely between without any affect on program speed. In fact, they are actually the [same](https://github.com/phoo-lang/phoo/blob/e03dead92b045b539fdbeb2ea68e610d9affa973/lib/_builtins.js#L114-L115) [functions](https://github.com/phoo-lang/phoo/blob/e03dead92b045b539fdbeb2ea68e610d9affa973/lib/_builtins.js#L134-L135) under the hood. The convention is to use `:::phoo do`/`:::phoo end` when enclosing a block of code that will just be run verbatim, and `:::phoo [`/`:::phoo ]` when enclosing a sub-array that will be interpreted as data or as a lambda partial that will have more code concatenated into it. Phoo also doesn't care if you open an array with `:::phoo [` and close it with `:::phoo end` -- although if I ever get around to writing a Phoo linter, it *will* complain if you do this!

The first caveat of sub-arrays is that sub-arrays are always assumed to be blocks of *code*, not *data*, and are run unless you tell Phoo otherwise. Consider this array:

```phoo
[ 1 2 3 4 5 ]
```

If you run this, the output will be a little misleading. The resultant stack printout says `Stack: [1, 2, 3, 4, 5]` -- and if you say "Yes! The brackets worked!" you'd be wrong. Leave that shell window open and type `:::phoo drop` to remove the top (righmost) item of the stack. You should get `Stack: [1, 2, 3, 4]`.

Now why was the last item of the array taken out? The answer is that the numbers were never in an array. Phoo interpreted the sub-array as a block of code, jumped into it, and "executed" 1, 2, 3, 4, 5, which being literals, simply pushed their value to the stack.

The correct way to put an array on the stack is with the word `:::phoo '` (quote), like so:

```phoo
' [ 1 2 3 4 5 ]
```

The output from this should be `Stack: [[1, 2, 3, 4, 5]]`, indicating that there is one array (inner set of brackets) on the stack (outer set of brackets). `:::phoo drop` here and you'll wind up with `Stack empty.`, indicating that there was only one item on the stack.

## Look Both Ways Before You Cross

The word `:::phoo '` is the simplest of a family of words known as "lookahead" words. Lookahead here means that it takes the item ahead of it (whatever it is) and operates on it, instead of allowing Phoo to run it normally. The behavior of `:::phoo '` is pretty mundane -- it does nothing but put the item on the stack. However, as shown above, this is useful to prevent Phoo from running things it shouldn't.

Another word that uses lookahead is `:::phoo to`. `:::phoo to` is used to define a new word so that Phoo will understand it. `:::phoo to` actually utilizes lookahead twice -- the first item after it is the new name for the word being defined, and the second item is the definition. Here's a nicely-formatted example:

```phoo
to hello do
    $ "Hello, World!" echo
end
```

Run that in the shell and nothing prints out -- indicating that the code inside the `:::phoo do`...`:::phoo end` wasn't run (which, if it had been, would print out `Hello, World!`). But something did happen -- type `:::phoo hello` at the prompt (autocomplete may help you), and you will be greeted by `Hello, World!`.

What we've done here is define a new word, `:::phoo hello`, that when run, prints `Hello, World!`. It's as simple as that. Type `:::phoo hello hello` and you will get `Hello, World!` twice.

Now, not every word that uses lookahead will *only* use lookahead as does `:::phoo to` -- it is perfectly legal for a word to take some inputs from the stack and others as lokahead, and process them all to produce a result. An example of this is `:::phoo times`, which takes an item from the stack (a number, N) and an item ahead (a block of code), and runs the lookahead block N times. Here's an example:

```phoo
10 times do
    $ "Looping!" echo
end
```

## The Work Stack, Extra Stacks, and The Other Stack

By now you should be familiar with the fact that Phoo utilizes a stack for manipulating values. This is the main "work" stack -- but it's not the only stack. The special word `:::phoo stack`, when placed at the front of a definition, turns the definition from a function that runs its code into an ancillary stack that stores data in its elements. For example,

```phoo
to mystack [ stack 0 1 2 ]
```

This creates an ancillary stack called `:::phoo mystack` with the elements 0, 1, and 2 on it to start with. Now with a stack, you can write such things as `:::phoo 1 mystack put` to push another 1 onto `:::phoo mystack`, or `:::phoo mystack take` to pop the top item of `:::phoo mystack` and leave it on the main work stack. The builtin words have their own stacks that they use for various purposes here and there. Ancillary stacks act a lot like local variables, allowing you to put it on a separate stack for safe keeping while you do stuff with other values, without cluttering the main stack and jumbling your program with stack-manipulating words, and forgetting where values are on the stack.

How this is actually accomplished is that when `:::phoo stack` is run, it pushes a reference to the sub-array it is sitting in to the work stack, and skips everything else after it. Now, interestingly, `:::phoo stack` does **not** use the lookahead operator.

The Phoo interpreter has another stack as well as the work stack.
