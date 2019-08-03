---
layout     : post
author     : Alan Tai
date       : 2019-08-03
title      : 16 deadly code smells - Part 1
subtitle   : Code smells that are considered fatal during coding interviews
image      : posts/2019/08/03/simpsons.png
categories : Programming
---
Every programming language has one or more [coding style guidelines](https://en.wikipedia.org/wiki/Programming_style). All developers know that but some of them don't care. They practice bad programming habits for years without realizing the importance of the issues they caused.

As one of the hiring managers at [Zuhlke Hong Kong](https://www.glassdoor.com.hk/Reviews/Z%C3%BChlke-Reviews-E451902.htm), I've seen a lot of [code smells](https://en.wikipedia.org/wiki/Code_smell) during [coding interviews](https://alantai.dev/interview/2019/06/30/a-quest-to-hire-great-software-engineers.html). Here are the top 16 bad coding practices organized into 6 categories: coding style, dirty code, testing, error handling, code complexity, and optimization.

We cover the first 8 code smells in [Part 1](https://alantai.dev/programming/2019/08/03/16-deadly-code-smells-part-1.html) and the rest in [Part 2](https://alantai.dev/programming/2019/08/04/16-deadly-code-smells-part-2.html).

## Coding styles

Ignoring the importance of coding styles usually indicates the lack of experience in creating software and reading other people's code. It's hard for inexperienced developers to understand the benefits of using a consistent coding style.

### 1. Inconsistent coding style

Consistency is one of the most dominant attitudes that good developers have. Being consistent is a result of logical thinking, which is a core element in software development. Every action that a developer chooses to take is because of some reasoning that the developer believes that it is true. Given the same environment, with the same input, we expect the same output. This is the same for coding style.

Let's compare the following snippets. Only one of them I consider as consistent.

**Snippet 1:**

```java
if(document.ownerId == user.id) {
    doSomething();
}
```

**Snippet 2:**

```java
if (document.ownerId == user.id){
    doSomething();
}
```

**Snippet 3:**

```java
if (document.ownerId == user.id) {
    doSomething();
}
```

**Snippet 4:**

```java
if(document.ownerId== user.id) {
    doSomething();
}
```

**Snippet 5:**

```java
if(document.ownerId==user.id){
    doSomething();
}
```

*Answer: Snippet 3*

Compilers ignore most of the spaces we used in our code, so what are they for? Readability.

If you use a space between `)` and `{` for better readability, then why wouldn't you also use a space between `if` and `(` as illustrated in snippet 1?

### 2. Inconsistent naming

Finding good names is not easy. It usually takes me a considerable amount of time. It's part of the process to create good software. Some people don't agree and they name their variables or methods like this:

```javascript
let value = 1;
let curValue = 0;
let previousVal = -1;
```

"Current" is represented in its short form in `curValue`, but this is not the case for "previous". Instead, "value" is shorten as `val` in `previousVal`. The flow of the mind of these people is so random that no one can explain the logic behind such naming style.

## Dirty code

This is the exact opposite of clean code. When you see dirty code, you can feel its ugliness and want to look away immediately. You feel so uncomfortable that it makes you hard to breath. It stinks and lingers for at least an hour.

### 3. Duplicate code

Duplicated code almost always indicates a failure in software design. So why does duplication ever exist? Some reasons I've heard from [coding interviews](https://alantai.dev/interview/2019/06/30/a-quest-to-hire-great-software-engineers.html) are:

* They think they have no choice but the problem to be solved requires duplication
* They believe the easiest way to solve a certain problem is by duplication
* Some don't even realize there is duplication

All of the above are the result of incompetence in software design.

### 4. Bad naming

[![Self-explanatory names](https://www.commitstrip.com/wp-content/uploads/2016/09/Strip-Le-stagiaire-et-la-variable-english650-final.jpg)](https://www.commitstrip.com/en/2016/09/01/keep-it-simple-stupid/)

Who needs comments when names are self-explanatory and logic is clear?

Examples of bad names I saw in [coding interviews](https://alantai.dev/interview/2019/06/30/a-quest-to-hire-great-software-engineers.html):

* `retVal`: I cannot find an even less meaningful name for this. All values returned by functions are called return-value. So what is this particular retVal referring to?
* `tmp`: Almost every variables within a function scope is short-lived and temporary. So how temporary should one consider it as temporary? Even a temporary variable deserves a more meaningful name.
* [Hungarian notation](https://en.wikipedia.org/wiki/Hungarian_notation): Variable or function prefixing to indicate and check for types is no longer needed for all modern IDEs. Type prefixing makes it harder to change type and could lead to type inconsistency. It also reduces readability.

### 5. Dead code

There are two kinds of dead code:

* **Unreachable code**: Code that within a condition that can't happen
* **Commented code**: Code that you are afraid to throw away

Unreachable code is less frequently seen during [coding interviews](https://alantai.dev/interview/2019/06/30/a-quest-to-hire-great-software-engineers.html) because the problem to be solved isn't that complicated.

One the other hand, commented code is a bigger issue that usually indicates a lack of understanding of source control.

If you ever want to comment code and think that you may need it at a later time, a better alternative is to use Git. For some IDEs, such as those made by IntelliJ, they provide local source control by default and the full history of your code is committed locally automatically. No one ever needs to comment any code.

## Testing

[![Where are the tests?](https://www.commitstrip.com/wp-content/uploads/2017/02/Strip-Ou-sont-les-tests-unitaires-english650-final.jpg)](https://www.commitstrip.com/en/2017/02/08/where-are-the-tests/)

Every piece of sufficiently complicated code needs proper tests. Some people test manually, some test their code by logging, and good engineers write unit tests.

### 6. No tests

More than half of the candidates I've met in [coding interviews](https://alantai.dev/interview/2019/06/30/a-quest-to-hire-great-software-engineers.html) didn't write any unit tests. Some refused to write unit tests even after they are requested to do so. One simply cannot be sure their code works without any tests. Even if their code appears to work, there is no way to be sure it still works after refactoring.

### 7. No meaningful tests

Writing unit tests is the first step to become better engineers. The next step is to write meaningful tests. Having tests that cover every single possible outcome is not meaningful and wastes time. Having tests that cover a few random cases is not any wiser.

Good tests should cover a few cases of [happy paths](https://en.wikipedia.org/wiki/Happy_path) and all [edge cases](https://en.wikipedia.org/wiki/Edge_case). The ability to design good tests is one of the assessments for our [technical interviews](https://alantai.dev/interview/2019/06/30/a-quest-to-hire-great-software-engineers.html).

### 8. Console logging

It is also called Caveman debugging. It is a popular debugging technique decades ago and is the preferred way of debugging in extreme situation such as kernel programming where you don't have any better alternatives.

Those candidates who print intermediate values to console would argue that console logging is an easier and faster way to debug. It is almost certain that they didn't know how to write unit tests and it would take longer time to complete the coding test during [technical interviews](https://alantai.dev/interview/2019/06/30/a-quest-to-hire-great-software-engineers.html).

Writing unit tests is as easy as printing to console, if not easier, once you learned how to do it. Additionally, unit tests are needed for refactoring in which console logging is often removed because it reduces code readability.

If someone would consider printing anything to console, that means there exists some code that is hard to understand. This is the case when unit tests are exactly for.

[Continue with numbers 9 to 16 code smells in Part 2.](https://alantai.dev/programming/2019/08/04/16-deadly-code-smells-part-2.html)

---

The important thing is to recognize code smells, especially in your own code. These are some of the very best books on how to write code that rocks:

* [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) by [Uncle Bob](https://en.wikipedia.org/wiki/Robert_C._Martin)
* [Code Complete: A Practical Handbook of Software Construction](https://www.amazon.com/Code-Complete-Practical-Handbook-Construction/dp/0735619670) by [Steve McConnell](https://en.wikipedia.org/wiki/Steve_McConnell)
* [The Art of Readable Code: Simple and Practical Techniques for Writing Better Code](https://www.amazon.com/Art-Readable-Code-Practical-Techniques/dp/0596802293) by [Dustin Boswell](https://www.oreilly.com/pub/au/4724)
* [The Pragmatic Programmer: From Journeyman to Master](https://www.amazon.com/Pragmatic-Programmer-journey-mastery-Anniversary/dp/0135957052) by [Andrew Hunt](https://en.wikipedia.org/wiki/Andy_Hunt_(author)) and [David Thomas](https://en.wikipedia.org/wiki/Dave_Thomas_(programmer))
