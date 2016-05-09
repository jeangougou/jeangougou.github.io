---
layout: post
title:  "git with class - part 1"
date:   2016-05-14 15:44:23 +0000
categories: git
description: "details about git commands"
published: true
tags:
- git
---

One thing I need to mention before the article is that every git command will imply the use of a bash-like terminal. UIs for source-control are notoriously less flexible and lack some of the more advanced functionality altogether. Also familiarity with _git_ itself is required to understand the following.

### It has to be someone's fault

Who's to blame for this piece of code ?  
Who could possibly ever come up with anything that complicated ?
Why is there a singleton in here ?

Ever asked yourself any of those questions during a code review ? I did. Many times over, many different occasions.
Fixing bugs is more important than delivering new features, but it is fair to allow who made the initial mistake to fix it too. It's a good chance to learn.

```git blame``` provides exactly this functionality. Who authored each line and when.

What really makes this command useful is its flexibility. Just by adding ``` -L 3,6 <FILENAME>``` for instance we can inspect the commits affecting only line 3 to 6. It can also identify and filter text _moved_ (```-M```) or _copied_ (```-C```) within or across files. Mind that most of the filters are disabled by default so it will take a bit to get up to speed with the various enabling command-line flag, but it is worth the effort.

In case the bug has not been found yet, there's a command which will definitely help us uncover everything. We only need a reference of a previous commit when a feature was working and one when it isn't any longer. ```git bisect``` acts as a [binary search][binary-search-algo] allowing to test each version to confirm the bug.

A step by step git bisect will look more or less like:

```
> git bisect start
> git bisect bad <BAD_REVISION>
> git bisect good <GOOD_REVISION>
Bisecting: 186 revisions left to test after this (roughly 7 steps)
[6f6976586aea159ff538ab556934a4458fe9f95] Merge branch 'mergetool'
```
After testing the functionality to confirm the bug it is possible to continue the search by if not found by marking the current step through

```
> git bisect good
```

Otherwise, the search is complete and the first ```git bisect bad``` mark can be issued.
It's also possible to skip a commit using ```git bisect skip```. While ```git bisect reset``` will revert things to normal again by synching to the previous branch. Stopping at the bad commit can also be done by typing ```git bisect reset HEAD``` in the command line.

If the test can be scripted it may be convenient to do so and execute ```git bisect run <TEST_SCRIPT>``` which will run for each commit.

### The Stash



[binary-search-algo]:https://en.wikipedia.org/wiki/Binary_search_algorithm
