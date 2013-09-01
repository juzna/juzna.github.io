---
layout: post
title: Continuous Integration testing for Nette framework (with TravisCI)
original_link: http://forum.nette.org/en/1073-continuous-integration-testing-with-travisci
original_link_name: Nette Forum
---


## Intro
We've got a decent number of **test cases**, which helps us to avoid many errors. They cover most essential functionality of the framework, and they try to cover as many use cases as possible. When they **fail**, it indicates an essential part of the framework is **not working** properly.

Before any change gets into *master branch* or into a *release*, <acronym title="does anyone really does this???">someone</acronym> should run the *test cases* and make sure nothing got broken. Ideally every developer should run them before sending a *pull request*. Otherwise, a *pull request* which breaks something is usually useless and only adds boring work to the one who is trying to merge it.

I dare to estimate that number of contributors who run all tests cases before a <acronym title="Pull Request">PR</acronym>, who check they didn't break anything, and who do it all for various versions of PHP, is *zero*. Not approximately, but precisely zero! On the other hand, the number of those who merge PRs is *approximately <acronym title="which is much better than precisely zero, but still bad">zero</acronym>* ;).


One may say that he sometimes wants to *merge* on iPhone and that it is *not possible* to check the tests there, but that's not correct. It's possible to merge safely even on iPhone...


Let's recap.



## Problems
- difficult to tell if a change broke something
- breaking change can get to master or a release
- difficult to check if a pull request passes
- running test is time consuming, and boring
- different versions of PHP
- existing tests are failing, but nobody cares


That being said, *test cases* are half useless when we cannot rely on them.



## Goals
- see whether *master branch* is passing, or which *test cases* don't; always, immediately, with history
- get notification when a change causes a test broken
- in a fork, let the developer know about test failure before he creates pull request
- easy way to check if a pull request is passing; i.e. immediately
- all that for different versions of PHP



## Solution
**Continuous Integration** with [Travis CI](http://travis-ci.org/).

It means **automatically** executing all test cases. After every push. Also available for *forks*. And also for *pull requests*. And for various versions of PHP.



## Implementation
*Travis CI* has a [tutorial](http://about.travis-ci.org/docs/user/getting-started/) on how to enable it for a project. The only requirement is to add file `.travis.yml` to project root directory and define [PHP related configuration](http://about.travis-ci.org/docs/user/languages/php/.)

This file is probably also the only **disadvantage** - it's one more file in project root directory, which is not inherently related to the framework itself. It may not seem like a problem, but one file here and another there and we'll end up with lots of mess in root like [symfony](https://github.com/symfony/symfony.)

Note that `.travis.yml` is helpful only in git/GitHub repository and has no meaning in distribution packages. Therefore it should be removed by *build-tools*.



## Limitation
We need to have **all** existing **tests passing** to gain all benefits. Or at least they have to be skipped, so that final result is *passing*. Only then, when something gets broken, we'll get *failed* result which will indicate a problem.



## Current status
There are many *test cases* [failing](http://travis-ci.org/#!/juzna/nette/builds/2254542), [some](http://travis-ci.org/#!/juzna/nette/jobs/2254544/L95) due to [bugs](https://bugs.php.net/bug.php?id=61484) in PHP itself.
These would need to be marked as *skipped* for that particular version of PHP.

The rest of *test cases* needs to be fixed to *pass*.



## Summary
There are pros and cons for this solution, but I believe it's worth having it included. If that happens, we'll be able to spot problems early and save our time.

[Pull Request](https://github.com/nette/nette/pull/636) is available, but still needs improvements.

So, do we want *TravisCI* to run tests for *Nette*?


