---
layout: post
title:  "git with class - part 1"
date:   2016-05-09 16:04:23 +0000
categories: git
description: "details about git commands (blame, bisect and stash)"
published: true
tags:
- git
---

Deepen your git knowledge with the following posts:  
[A git primer](http://jeangougou.github.io/git/2016/05/07/a-git-primer.html)  
_[Git with class pt 1](http://jeangougou.github.io/git/2016/05/09/git-with-class-pt1.html)_  
[Git with class pt 2](http://jeangougou.github.io/git/2016/05/10/git-with-class-pt2.html)  
[Git with class pt 3](http://jeangougou.github.io/git/2016/05/16/git-with-class-pt3.html)  

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

While working on a feature branch, when priorities shift, there's the need to quickly move from a branch to the next. Often the state of things is not ready to be committed anywhere. For this occasions ```git stash``` is the tool to use.  
Stash is a temporary local repository of changes that can be re-applied on top of the current local state at any time.  
Another sample of common workflows:  

```
# move current pending changes into the stash stack
> git stash
#
# change branch, do other thigs, go back to the previous branch
#
# and apply the changes from before
> git stash pop

```

There are more complicated scenarios, obviously, and in some cases we end up saving multiple elements in the stash.
For a list of available saved changes we can execute ```git stash list```. It's nice to notice that the default description in git is self-explanatory _WIP on <BRANCH_NAME>_ (wip as in work in progress), but in case we want more descriptive names, it is possible to add a custom description through ```git stash save '<COMMENT>'```.  

### The wheel of time

Every single git user at one point has had the necessity of go back to a previous file revision. It's one of the main purposes of the tool one would expect, and yet there's always a bit of googling involved the first times because of some very easy concepts missing. Let's fix that. We all know that each change in git is marked by an hash value, which can be shortened most times to about an 8 characters string. That revision hash is the only element we need to sync to any point in time desired. We can use 3 different commands for that, ```checkout```,```reset``` or ```show```.
There are minor differences among them:

- ```show``` will print the content into the terminal, which allows to redirect it into a file tool
- ```reset``` will sync to a specific revision and drop untracked changes
- ```checkout``` will act as reset while keeping the pending changes

Syntax for the command is ```git reset/checkout/show <REVISION>```.

Quite easy, isn't it ?

[binary-search-algo]:https://en.wikipedia.org/wiki/Binary_search_algorithm
