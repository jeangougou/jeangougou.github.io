---
layout: post
title:  "git with class - part 3"
date:   2016-05-16 10:02:14 +0000
categories: git
description: "details about git commands (filter-branch, subtree and submodule)"
published: true
tags:
- git
---

Deepen your git knowledge with the following posts:  
[A git primer](http://jeangougou.github.io/git/2016/05/07/a-git-primer.html)  
[Git with class pt 1](http://jeangougou.github.io/git/2016/05/09/git-with-class-pt1.html)  
[Git with class pt 2](http://jeangougou.github.io/git/2016/05/10/git-with-class-pt2.html)  
_[Git with class pt 3](http://jeangougou.github.io/git/2016/05/16/git-with-class-pt3.html)_  

History can be rewritten. No needs for a time machine, _git_ can do that for you.
Out of the box a number of filters are provided for [git filter-branch][git-filter-branch] command.
It's a command that operates on one or more branches, or more precisely a [rev-list][git-rev-list], which lists commit objects in reverse chronological order. Git filter branch will apply a set of positive or negative rules, named filters, to the rev list. Therefore its bound only by the I/O limitation of the system it's running on, rather than having to wait for human input as happens in [git rebase][git-rebase] with ``` --interactive``` flag.

It is important to know that filter branch will re-commit every single committed change that match the filter specified, and new commits will have different object refs to the original ones, making it tricky to merge the rewritten branch on top of the original one.

There's a specific order in which filters get applied to the  revisions. First any environment variable through ```--env-filter``` if you want to rewrite want to rewrite the author/committer name/email/date/time (env names are ```GIT_AUTHOR_NAME```, ```GIT_AUTHOR_EMAIL```, ```GIT_AUTHOR_DATE```, ```GIT_COMMITTER_NAME```, ```GIT_COMMITTER_EMAIL```, and ```GIT_COMMITTER_DATE```).
After the environment, any change to the tree is considered with ```--tree-filter```; this one will ignore the  ```.gitignore``` file and, based on the current working directory, auto-add and auto-remove files to match what's on the local path. To achieve similar results and update the index without checking out the tree (for more complex index update operations see [git update-index][git-update-index]). The ```--parent-filter``` can be used to change the parent references and point a commit to different history object, even multiple ones to completely rewrite its origin.
With ```--msg-filter``` (```-amend``` on steroids), it is possible to rewrite commit messages in any point in the branch's history. Instead of implicitly calling [git-commit-tree][git-commit-tree] it's possible to use a custom commit filter with the ```--commit-filter``` parameter.
To edit tag names we can use ```--tag-name-filter```, which will strip each signature and never allow changes to the author, the timestamp, or the tag message. The subdirectory filter (```--subdirectory-filter```) will act only upon a subdirectory's history.

Some common usage for this powerful history-bending command following.

```
# remove a file containing confidential information from all commits
> git filter-branch --tree-filter 'rm -f secrets.txt' HEAD

# or a quick alternative affecting the list of revisions without checking out
> git filter-branch --index-filter 'git rm --cached --ignore-unmatch secrets.txt' HEAD

# making subfolder <SUBFOLDER> the new project root
> git filter-branch --subdirectory-filter <SUBFOLDER> -- --all

```

#### When subdirectories matter

We've just seen how to rewrite history to make a subdirectory a new project root, what if we need the opposite ?
There are cases, for instance, when I want to include a dependency into my repository to keep both up to date to their latest implementation.  
The easiest way is that of copying files manually, but then we lose the benefits of being able to pull from the remote sub-project in the future and we are stuck with an old version. Anyone who encountered version dependencies issues before knows that this is a bad idea.  
Alternatively it's possible to use the [subtree-streategy][subtree-streategy].
Another _git_ way of addressing this scenario uses [git-submodule][git-submodule] instead. This last strategy gives us a repository within a repository (like a [Matryoshka doll][Matryoshka_doll] or a [Fractal like structure][Fractal]).

Any of the listed options has pros and cons, Let's walk through them quickly.
File copying loses all references to other repositories, it cannot easily write back upstream and any update risks deleting internal changes that diverged from the original copy.

Subtrees allow for a single repository to exist containing every element needed

Submodules is the easiest to setup and manage due to native git tooling ([git-submodule][git-submodule]) but will also force you to keep track of multiple repositories in a hierarchical formation.

To figure out which one is best for your specific case scenario takes a bit of trial and error, also it depends highly on the familiarity of the rest of the team with this tools.

[git-commit-tree]:https://git-scm.com/docs/git-commit-tree
[git-rebase]:https://git-scm.com/docs/git-rebase
[git-rev-list]:https://git-scm.com/docs/git-rev-list
[git-filter-branch]:https://git-scm.com/docs/git-filter-branch
[git-update-index]:https://git-scm.com/docs/git-update-index
[subtree-streategy]:https://www.kernel.org/pub/software/scm/git/docs/howto/using-merge-subtree.html
[empower-git]:https://github.com/stevemao/awesome-git-addons
[git-submodule]:https://git-scm.com/docs/git-submodule
[Matryoshka_doll]:https://en.wikipedia.org/wiki/Matryoshka_doll
[Fractal]:https://en.wikipedia.org/wiki/Fractal
