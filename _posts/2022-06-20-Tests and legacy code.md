---
layout: post
title: "3 types of Tests you need to know for Legacy Code"
author: paolo.venturi
categories: [ Legacy Code, Test, Refactoring, Software Development]
image: assets/images/tests_and_legacy_code/0.jpg
excerpt: "How automated tests let you feel more comfortable when touching Legacy Code"
---

Questo articolo è disponibile [anche in italiano.](https://studiofarma.github.io/CGM-Innovation-Hub-Tech-Blog/I-test-ed-il-legacy-code/)

Automated tests in software development are very important. They provide benefits during and after development.

There are many different types of tests; today we will look at the most useful when working with legacy code.

\
But let's start with the basics: why do automated tests help us develop software?

## Correctness

Tests by definition establish the correctness of code.

When we write a test we establish preconditions for the execution of the code to be tested.
Through assertions, we go to verify certain conditions that occur at the end of execution.

Knowing that these preconditions are met gives us greater confidence during development.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/tests_and_legacy_code/1.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://tenor.com/view/rhel1-captain-obvious-thank-you-hero-gif-11964508">
            Tenor
        </a>
    </em>
</center>

## Understanding

When we work with legacy code, tests help us understand the code.

To define a test we must instantiate classes and call functions that need parameters.

By analyzing existing tests, we have examples to help us better understand:

- how to instantiate classes
- behaviors and parameters of functions
- desired behaviors.

## Speed

Writing tests takes time so it slows down development.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/tests_and_legacy_code/2.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://memegenerator.net/instance/66040229/dwight-k-schrute-false">
            Meme Generator
        </a>
    </em>
</center>

\
Tests are a long-term investment but even in the short term they provide a big benefit.
They return quick feedback during development and this helps to stay focused.

An example: we need to test a function that determines whether a web address is correct or not.

<script src="https://gist.github.com/paoloventuri91/bbfb194ffcc477cef60f65acb0f71ac1.js"></script>

Without automated tests, we would have to manually run the function with different inputs and compare the result with the expectations.

With unit tests instead, we can verify many test cases in less than a second. See the following example.

<script src="https://gist.github.com/paoloventuri91/59026a1021316648bdb096fc0130ffee.js"></script>

Also, running the tests while writing the code makes it easier to locate any errors.

\
Let's now consider how different kinds of tests relate to legacy code refactoring.

## Acceptance tests

Acceptance tests define the behaviors that a feature should have from the end user's point of view.
It's the customers that ask for a change or the Product Owners who define the parameters to be met.

Acceptance tests are comparable to a contract and place at a medium/high level in the code.
Only after successful testing, the change can be "accepted."

Examples of acceptance tests are:

- assertions about the behavior of a GUI button
- assertions about the responses of an API given a certain input.

When we change code that we don't know, we may feel unconfident.
Acceptance tests if presents are very helpful to reduce this discomfort.

## Unit tests

Unit tests are so defined because they go to test a single software unit. A unit can be a class or even a function.

Having to cover such a small set of code makes these tests the best for locating errors.

Because they are placed at a low level in the code they are defined in a technical language.
So they are of little use to the end-user but very practical for the developer.

Unit tests must meet one basic condition: they have to run in isolation.

They are not unit tests:

- if they use Databases
- if they exploit the File System
- if they communicate over the network.

In a nutshell: tests must not depend on external conditions.

An example: testing a class that executes calls to an external API.
Tests should not call the API but simulate its execution and response.
Using the actual API, the outcome of the test will depend on:

- presence and speed of the network connection
- operability and state of the API.

But the goal of unit tests is to test code defined in a class or method.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/tests_and_legacy_code/3.jpg" width="500">
    <br>
    <em> Photo by
        <a href="https://imgflip.com/memegenerator">
            imgflip
        </a>
    </em>
</center>

\
Unit tests **must** be fast! This is another key feature.

Staying with the previous example: wait times using an external API could be very long.
This would lead us to run them less frequently, increasing the probability of inserting bugs.

Unit tests, like acceptance tests, help us understand the behaviors of the legacy code.
Compared to the acceptance, they go one level lower in our codebase.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/tests_and_legacy_code/4.jpg" width="800">
    <br>
    <em>
        Levels of Tests
    </em>
</center>

## Characterization tests

Characterization tests come into play when unit and acceptance tests do not exist.

They help in better understanding how an existing program works.
They allow us to describe the current behaviors of a program, so they are most useful with legacy code.

Often legacy code has characteristics that hinder testing:

- it does not provide values to test
- it is difficult to instantiate classes
- methods to be called need complex parameters.

In these cases the "Legacy Code Dilemma" arises:
to change the code we must have tests but to define tests we must change the code.

<center>
    <img src="{{ site.url }}{{ site.baseurl }}/assets/images/tests_and_legacy_code/5.gif" width="500">
    <br>
    <em> Photo by
        <a href="https://giphy.com/gifs/theoffice-the-office-tv-show-dw60YIVoqu0MZexouY">
            GIPHY
        </a>
    </em>
</center>

\
The dilemma is solved by making small "safe" changes useful for defining tests.
In this way, the probability of introducing bugs is minimized.

An example: we need to go test the following program that prints today's date.

<script src="https://gist.github.com/paoloventuri91/af2353eff905ec6e7ce12db55e706885.js"></script>

This code is not easily testable because the result does not depend on any input. Instead, the result changes depending on when the program is executed.
To test the logic we need to make a modification that removes this dependency.

<script src="https://gist.github.com/paoloventuri91/d032294cd5f40f59cef099b38342c782.js"></script>

Thanks to the ```FormatDate``` function we have isolated the logic of formatting the date.
With the parameter ```date```, moreover, we can specify an input of our choice.

At this point writing a test will be possible.

<script src="https://gist.github.com/paoloventuri91/89f3de7c513032c6b0bafc7bfcbafc5d.js"></script>

## Conclusions

Automated tests are very useful in software development and are essential if we have to work with legacy code.

Acceptance and characterization tests cover code at a higher level than unit tests.
Both levels are necessary.

In the next articles on legacy code, we will go over some useful techniques for arriving at refactoring techniques.

&nbsp;
&nbsp;

***

&nbsp;
&nbsp;

### References

- **Working Effectively with Legacy Code** - Michael C. Feathers, 2004 - <https://www.oreilly.com/library/view/working-effectively-with/0131177052>
- **Refactoring: Improving the Design of Existing Code** - Martin Fowler, 1999 - <https://martinfowler.com/books/refactoring.html>
