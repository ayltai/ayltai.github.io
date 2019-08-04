---
layout     : post
author     : Alan Tai
date       : 2019-08-04
title      : 16 deadly code smells - Part 2
subtitle   : Code smells that are considered fatal during coding interviews
image      : posts/2019/08/04/simpsons.png
categories : Programming
---
Every programming language has one or more [coding style guidelines](https://en.wikipedia.org/wiki/Programming_style). All developers know that but some of them don't care. They practice bad programming habits for years without realizing the importance of the issues they caused.

As one of the hiring managers at [Zuhlke Hong Kong](https://www.glassdoor.com.hk/Reviews/Z%C3%BChlke-Reviews-E451902.htm), I've seen a lot of [code smells](https://en.wikipedia.org/wiki/Code_smell) during [coding interviews](https://alantai.dev/interview/2019/06/30/a-quest-to-hire-great-software-engineers.html). Here are the top 16 bad coding practices organized into 6 categories: coding style, dirty code, testing, error handling, code complexity, and optimization.

The first 8 code smells are covered in [Part 1](https://alantai.dev/programming/2019/08/03/16-deadly-code-smells-part-1.html) and the rest in [Part 2](https://alantai.dev/programming/2019/08/04/16-deadly-code-smells-part-2.html).

## Error handling

[Murphy’s law](https://en.wikipedia.org/wiki/Murphy%27s_law) states that,

> Anything that can go wrong will go wrong

Error handling is simply part of all software. Every software created must have some form of error handling. Some inform users about the errors that occurred by displaying error messages. Some hide errors from users but still they log the incident somewhere for troubleshooting. But there are some developers who convince themselves that errors are imaginary and there is no need to handle them.

### 9. No-op error handlers

Some programming languages have the concept of [checked exceptions](https://en.wikipedia.org/wiki/Exception_handling#Checked_exceptions). It is required to handle those potential errors at compile time. I’ve seen a lot of developers who handle such case for the sake of satisfying the compiler. They catch the exception but decide to do nothing about it.

### 10. Error Driven Development

Similar to Test Driven Development, an error driven approach refers to the process of thinking about error handling and edge cases, implementing them first and develop the rest around them.

First things first. If you can’t get the happy path right, it is useless to handle any errors.

When you are given a problem to solve within a limited time, always aim to maximize the value of your deliverable. Get a partial solution first, then improve it.

### 11. Ignore error messages

[![It's not working!](https://www.commitstrip.com/wp-content/uploads/2016/03/Strip-Larroseur-arros%C3%A9-650-finalenglish-1.jpg)](https://www.commitstrip.com/en/2016/03/03/its-not-working/)

Not all error messages are useful, but some are really helpful. They tell you which line of code the error came from with detailed reason. Unfortunately, some developers choose to ignore them.

Whenever there is an error pops up in a console, they would immediately switch back to their IDE and attempt to find where it came from. Didn’t the error message just tell them exactly this?

## Code complexity

One of the ways to measure the complexity of software design is [cyclomatic complexity](https://en.wikipedia.org/wiki/Cyclomatic_complexity). A high complexity means it is difficult for human to understand. In other words, it will take a longer time to read code.

Good software designs are those with a low cyclomatic complexity. One of the major differences between junior and senior developers is the complexity of their code that solves the same problem. Junior developers tend to solve simple problems using complicated approaches, while their senior peers tend to solve the same problem using simpler solutions.

### 12. High conditional complexity

The more candidates I’ve met in coding interviews, the more obvious the pattern of how junior developers solve problems. The most common approach is to add a conditional check for every use case that they cannot generalize. This approach works, it usually solve the problem but in a bad and ugly way.

Candidates who passed [Zuhlke](https://www.glassdoor.com.hk/Reviews/Z%C3%BChlke-Reviews-E451902.htm)’s [coding test](https://alantai.dev/interview/2019/06/30/a-quest-to-hire-great-software-engineers.html) used a minimum number of conditional checks.

### 13. Special cases and magic values

Less experienced developers tend to see everything as special cases. In the process of implementing a special case, magic values are often created. For example, a developer would return a negative number such as -1 or -99 to represent something goes wrong with a function. Now -1 doesn’t mean -1 anymore, but something else. That’s really bad.

The more magic values created, the less chance a candidate would pass a [coding interview](https://alantai.dev/interview/2019/06/30/a-quest-to-hire-great-software-engineers.html).

## Optimization

Software should be developed in this order: get it work, then improve it, and make it fast. Most candidates in coding interviews would agree that solving the given problem is of highest priority. But that doesn’t mean that optimizations aren’t important, especially the most obvious ones.

### 14. Unoptimized code

There are unlimited ways to create unoptimized code if we are not aware of it. It could be something that executes slowly, or it could be something that waste computation power and memory.

In Java, we have the object equivalent of the primitive types. From time to time, I’ve seen someone who uses `Integer` instead of `int` for no reason.

For loops, consider extracting function calls inside loops to outside of the loop. That is something easy and obvious to do so let’s not convince ourselves that we can write dumb code and optimize it later. Such simple optimization should be done in the first place.

### 15. No meaningful optimization

Many developers stop solving a problem when they get their code working for the first time. They are satisfied with the magic they have created. Come on! That’s simple not true in almost all cases.

There are still a lot we can do after getting our code working. How would it be used? On what machines? What are the users? Could it be used in a multi-threaded environment?

Your code becomes unoptimized after you know more about its environment.

### 16. Using the wrong tools

It is important to use the right tools for the right problem. I would fail a candidate if they suggest to use Express framework just because they need to use JavaScript to create a simple counting algorithm that runs in console. Those who never understand what JavaScript is would refuse to use [Vanilla JS](http://vanilla-js.com/). To them, JavaScript is Express.

Don’t give me a forest if I only ask for a tree.

[Review numbers 1 to 8 code smells in Part 1.](https://alantai.dev/programming/2019/08/03/16-deadly-code-smells-part-1.html)

---

The important thing is to recognize code smells, especially in your own code. These are some of the very best books on how to write code that rocks:

* [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) by [Uncle Bob](https://en.wikipedia.org/wiki/Robert_C._Martin)
* [Code Complete: A Practical Handbook of Software Construction](https://www.amazon.com/Code-Complete-Practical-Handbook-Construction/dp/0735619670) by [Steve McConnell](https://en.wikipedia.org/wiki/Steve_McConnell)
* [The Art of Readable Code: Simple and Practical Techniques for Writing Better Code](https://www.amazon.com/Art-Readable-Code-Practical-Techniques/dp/0596802293) by [Dustin Boswell](https://www.oreilly.com/pub/au/4724)
* [The Pragmatic Programmer: From Journeyman to Master](https://www.amazon.com/Pragmatic-Programmer-journey-mastery-Anniversary/dp/0135957052) by [Andrew Hunt](https://en.wikipedia.org/wiki/Andy_Hunt_(author)) and [David Thomas](https://en.wikipedia.org/wiki/Dave_Thomas_(programmer))