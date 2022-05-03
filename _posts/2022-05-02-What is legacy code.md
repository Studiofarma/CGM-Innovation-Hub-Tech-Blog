---
layout: post
title:  "What is legacy code?"
date:   2022-05-02 12:25:12 +0200
categories: jekyll update
---

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/what_is_legacy_code/0.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://en.wikipedia.org/wiki/Punched_card#/media/File:Blue-punch-card-front-horiz.png">
            Wikipedia
        </a>
    </em>
</center>

\
In software projects, as in other fields, it is important to keep low costs and development time.\
The time of implementing a new feature or fixing a bug depends on the status of the existing codebase.\
Estimating the effort of changing simple and clean code is easy. While it's harder to make predictions with legacy code.\
This results in inflated estimates and costs that grow exponentially.

**But what is legacy code?**\
**How many people can distinguish legacy code, and avoid writing it?**

\
To answer these questions, I did what anyone would do: I googled.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/what_is_legacy_code/1.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://www.meme-arsenal.com/memes/58b301f6612908a498826a64b4937dd6.jpg">
            Meme-arsenal
        </a>
    </em>
</center>

\
I found many definitions of "legacy code" on the internet:

- code written by others or inherited (<https://stackoverflow.com/a/4174886>)
- "old" code (<https://en.wikipedia.org/wiki/Legacy_system>)
- code that uses unsupported technologies (<https://enkonix.com/blog/legacy-code>)
- badly structured code (<https://www.oreilly.com/library/view/working-effectively-with/0131177052>)
- code without tests (<https://understandlegacycode.com/blog/what-is-legacy-code-is-it-code-without-tests>)

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/what_is_legacy_code/2.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://www.meme-arsenal.com/memes/58b301f6612908a498826a64b4937dd6.jpg">
            Meme-arsenal
        </a>
    </em>
</center>

\
These definitions don't converge on a clear agreement about legacy code, and how it relates to the costs of maintaining a software project.\
Let's analyze them one by one.

&nbsp;
&nbsp;

## Code written by others or inherited

Working on code that we didn't write ourselves is tricky:

- Variables and classes names may not be evocative or appropriate.
- The structure of the code, files, and classes is not familiar.
- Maybe the author used a design pattern that we don't know.

\
Is this enough to define code written by others as legacy?

**NO!**

If the developer has made it easy to understand, then it cannot be defined as legacy code.

&nbsp;
&nbsp;

## "Old" code

Does the age of code enough to define it as legacy?

**NO!**

\
As for code written by others, this is not enough to get a definition.

Can we establish a period beyond which the code becomes old?\
It is not possible. A code that was written a month ago may be more confusing than a code written a year ago.\
It all depends on how we cared for it and made it "simple".

Let's consider a trivial example.
A program that calculates the Fibonacci sequence: each number of the sequence is the sum of the previous two numbers.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/what_is_legacy_code/3.jpg" width="500">
</center>

Let's see implementations in two different programming languages: the dear old C and the more recent C#.

### Fibonacci sequence in C

{% gist 75bd7dd18ac985e32ad0278c1cc6bea2 %}

### Fibonacci sequence in C\#

{% gist ea17731fdd0de434af6a80be7d5b492e %}

These two programs do the same thing but the differences are huge.

The C program takes advantage of recursion, making the `main` function more readable.
Functions and variables have proper names.

In contrast, the C# program uses an array and a for loop to calculate numbers in the sequence.\
Variable names are generic and all the logic is in the `Main` function.

We can agree that the age of the code and the language, are not enough to define it as legacy.

&nbsp;
&nbsp;

## Code that uses unsupported technologies

What about code that uses libraries that are out of support? Can it be defined as legacy? **YES AND NO...**

Unsupported libraries expose us to a risk.
In case of bugs or missing features, there are only two possible ways:

- If the library is customizable, we can provide missing implementations by ourselves.
- Otherwise, the only solution is to replace it with a library that suits our needs.

Replacing a library can be very complicated, unless we adopted it in well-isolated locations.
In this case, the switch could be almost painless.

The use of outdated technologies is not enough to define code as legacy.\
But it can cause a lot of inconveniences.

&nbsp;
&nbsp;

## Badly structured code

Can we define badly structured code, e.g. a monolith, as legacy? **YES!**

Lack of separation of responsibilities, classes with complicated dependencies, non-evocative names.\
These and other problems (code smell) make our code "weak".

Code smells are definitely one of the reasons we define code as legacy.

&nbsp;
&nbsp;

## Code without tests

Is the lack of tests enough to define code as legacy? **YES!**

We can write well-structured code, without code smell and with popular and recent libraries.\
But if our codebase is not covered by a collection of tests we will be exposed to the risk of introducing bugs with every change.\
Tests are essential to define the required behaviors of our software.\
They protect us from possible errors and help us to better understand the logic of the code they cover.

&nbsp;
&nbsp;

## So?

In the end, a short and effective definition might be:
**legacy code is untested code that is hard to change.**

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/what_is_legacy_code/4.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://www.hallofseries.com/wp-content/uploads/2019/01/Immagine-1.png">
            Hall of Series
        </a>
    </em>
</center>

\
In the next articles of the series, we're going to see how to work with legacy code and how to make it "less legacy" by testing and refactoring.

&nbsp;
&nbsp;

***

&nbsp;
&nbsp;

### References

- **Working Effectively with Legacy Code** - Michael C. Feathers, 2004 - <https://www.oreilly.com/library/view/working-effectively-with/0131177052>
- **Refactoring: Improving the Design of Existing Code** - Martin Fowler, 1999 - <https://martinfowler.com/books/refactoring.html>
