# Phoo Tutorial

This page serves as a quick introduction to the programming language Phoo and how to write code in it. While Phoo is definitely new, it is a full-featured language, and once the hurdle of reverse Polish notation is overcome, Phoo code is quick and rewarding.

*This tutorial is adapted in part from the tutorial in* [The Book of Quackery](https://github.com/GordonCharlton/Quackery/blob/main/The%20Book%20of%20Quackery.pdf), *by Gordon Charlton, due to the fact that Phoo is adapted in part from Quackery.*

[TOC]

## Getting Started

Phoo is not yet distributed as an executable file. However, a simple online shell has been jimmied up at <https://phoo-lang.github.io/>, so head over there to try it out. You should get something like this:

```txt
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
$ "Hello World!" echo
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

Hooray! It worked. The twos were pushed onto the stack and `+` added them, leaving the answer, 4, on the stack.

TODO: FINISH
