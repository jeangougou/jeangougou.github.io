---
layout: post
title:  "git with class - part 2"
date:   2016-05-10 10:02:14 +0000
categories: git
description: "details about git commands (request-pull, format-patch, am)"
published: true
tags:
- git
---

Deepen your git knowledge with the following posts:  
[A git primer](http://jeangougou.github.io/git/2016/05/07/a-git-primer.html)  
[Git with class pt 1](http://jeangougou.github.io/git/2016/05/09/git-with-class-pt1.html)  
_[Git with class pt 2](http://jeangougou.github.io/git/2016/05/10/git-with-class-pt2.html)_  
[Git with class pt 3](http://jeangougou.github.io/git/2016/05/16/git-with-class-pt3.html)  

#### Pull requests

Everyone loves to create pull requests, don't they ? It's the clean powered engine that runs many OS project.
However it is sometimes quite tricky to figure out what's happening and the process can be quite confusing.
Assuming we are running under a common hotfix scenario, where master has been patched and we want to propagate those changes to a development branch _dev_. Check out the [official git request-pull documentation][git-request-pull].

```
# A version v1.0 has been released
# hotfixes have been added to the master branch
> git push origin master

# we want to propagate those changes to the release v1.0
> git request-pull v1.0 origin master
```

There's a trick to generating a pull request correctly every time: read it from right to left.  
What would happen if I wanted to merge *master* branch in *origin* repository into *v1.0* ?
That's the question ```git request-pull``` answers. It's important to notice that the command does not operate upon the remote repository but generates only the expected list of diffs. This is also probably why most web UIs allow for very comfortable buttons to create pull requests through a wizard. Same trick of reading the source on the right and the destination on the left applies there too.

#### Patches

What if we don't have access to the remote ? A not-so-common approach is that of generating patch files and email them to the interested party. Local commits can be easily bundled up into patch files, specified by the starting _[PATCH]_ tag.

```
# creating a patch with the last commit
> git format-patch -1
=>
  0023-last-commit-message.patch
```

If we are on the other side of the fence, receiving a patch file we can apply its changes and validate its content.

```
# shows what will happen if applied, does not apply
> git apply --stat 0023-last-commit-message.patch

# checks for issues
> git apply --check 0023-last-commit-message.patch

```

Patches are easy to handle because they allow an easily visible of the changed state and they can be [signed off][git-am] with ```git am --signoff < 0023-last-commit-message.patch ``` which will also apply it to the current branch.

[git-am]:https://git-scm.com/docs/git-am
[git-format-patch]:https://git-scm.com/docs/git-format-patch
[git-request-pull]:https://git-scm.com/docs/git-request-pull
[Distributed Git - Contributing to a Project]:https://git-scm.com/book/ch5-2.html
