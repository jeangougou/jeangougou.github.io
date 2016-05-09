---
layout: post
title:  "A git primer"
date:   2016-05-07 13:04:45 +0000
categories: git
description: "quickstart guide on git collaboration"
published: true
tags:
- git
---

There are many online resources covering the getting started phase for git.

[Simple getting started](http://rogerdudler.github.io/git-guide/)

[Git Howto](http://githowto.com/)

[Official getting started](https://git-scm.com/book/en/v1/Getting-Started)

Pick any of the 3 listed above, install your git utilities and start by typing in console:

```
git --version
```

to read what installed version you currently have and confirm that the installation was successful.


#### Configuration

Any git instance has one global .gitconfig file and a local one which will overwrite global configuration.
It's always a good idea to have a solid and reliable .gitconfig file.

**Windows Sample**  
{% highlight python %}

[core]  
	autocrlf = False  
	repositoryformatversion = 0  
	filemode = false  
	bare = false  
	logallrefupdates = true  
	symlinks = false
	ignorecase = true
	hideDotFiles = dotGitOnly
	whitespace = cr-at-eol
	pager = less -x4
[color]
	ui = auto
[credential]
	helper = !\"C:/Program Files (x86)/GitExtensions/GitCredentialWinStore/git-credential-winstore.exe\"
[user]
	name = FirstName.Surname
	email = FirstName.Surname@mailprovider.com
[merge]
	tool = kdiff3
[mergetool "kdiff3"]
	path = C:/Program Files (x86)/KDiff3/kdiff3.exe
[diff]
	guitool = kdiff3
[difftool "kdiff3"]
	path = C:/Program Files (x86)/KDiff3/kdiff3.exe
[fetch]
	prune = true
[push]
	default = current
[url "https://"]
	insteadOf = git://
[format]
	pretty = %C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n%C(white)%s%C(reset) %C(bold white)— %an%C(reset)
[help]
	autocorrect = 1
[filter "lfs"]
	clean = git lfs clean %f
	smudge = git lfs smudge %f
	required = true
[alias]
	st = status
	co = checkout
	br = branch
	up = rebase
	ci = commit

{% endhighlight %}

Most of the instructions there are valid across multiple instances. Feel free to change the path to your favorite 3-way comparison tool. I like [KDiff3](http://kdiff3.sourceforge.net/) also because it's cross platform, but pick your favorite tool ([1](https://gist.github.com/shawndumas/6158524)).


#### Expected Workflow

We will be using [Gitflow workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) on top of a [Forking workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/forking-workflow)

So whenever you partake in a new project the first thing to do would be to fork the main project, and add one repository per partecipant.

Let's say I'm forking [protobuf](https://github.com/google/protobuf);

That would be your main _Team_ repository (if you worked a google that is).
Once that's done I will have a new remote with my username, so instead of:

```
https://github.com/google/protobuf.git
```

I will have:

```
https://github.com/<MY USER NAME>/protobuf.git
```

to check the list of my current remotes, at any time, the command would be:

```
git remote --verbose
```

Now, once forked, you have at least 2 remotes to point to; the main one from which you forked to is commonly named *upstream*, while my one (the one with my user name in the url) would be the origin.
Based on this quick description, I already know that I should clone the _git_ repository locally from my forked remote and add the upstream remote ad an additional one.

As a reference, when describing the current remotes, initially I expect to see something along these lines:

```
λ> git remote --verbose
origin  https://github.com/<MY USER NAME>/protobuf.git (fetch)
origin  https://github.com/<MY USER NAME>/protobuf.git (push)
upstream        https://github.com/google/protobuf.git (fetch)
upstream        https://github.com/google/protobuf.git (push)
```

###### Wait, how do I add a remote ?

There is a simple instruction, where ALIAS is the local name reference and URL points to the remote .git repository

```
git remote add <ALIAS URL>
```

###### I have a basic setup, how do I actually contribute to a repository ?

Assuming you already have a little familiarity with *git*, let's see what are the general steps to commit in the _team_ repository.

1. Sync to your expected initial source
2. Create a new feature branch
3. Commits can be pushed to the remote origin reference of that same feature
4. Test locally
5. Create a pull request from origin/<Feature branch> to upstream/<release branch>
6. Ask someone else to code review it
7. Answer to their comments on the pending pull request and keep pushing commits to address the issues if any is present
8. Once approved the pull request will be merged into upstream/<release branch>
9. Sync your current instance to the latest one un upstream, both locally and on origin

To give a quick example of the workflow commands that you might want to use:

```
# sync your local and remote dev branch to the upstream one
git checkout dev
git pull upstream dev
git reset --hard FETCH_HEAD
git push origin dev

# create a new feature branch
git checkout -b a_feature_branch

# commit changes once proven to work locally
git add --all .
git commit -m "implemented a_feature_branch"
git push origin a_feature_branch

# create a pull request
# ask someone to code review it

# (optional) update your current pending pull request
git add --all .
git commit -m "implemented a_feature_branch"
git push origin a_feature_branch

# core reviewer will merge your pull request
# sync again to the latest upstream/dev
git checkout dev
git pull upstream dev
# next line removes local changes
git reset --hard FETCH_HEAD
# makes sure your dev branch is in sync with the team one
git push origin dev

```

#### Pull requests got merged before mine and now I have conflicts

This can happen quite often, particularly when working in larger teams. As a general rule, you should always start from the latest available development branch, and occasionally pull the latest changes to keep being in sync while developing your feature.

Even with the utmost care it will happen sooner or later that merge conflicts arise.
Check again the *merge* and *diff* areas in the configuration sample.

In case conflicts arise you can always write the following command in console and follow the suggested steps:

```
git mergetool
```

It's an interactive operation, you can skip the 3-way comparison through and software of choice by picking *local*, *yours* or *theirs* in case of file changes or *keep*, *delete*, *rename* or *move* in case of a structure change in the git tree (file deleted, renamed or moved).

TIP: You can change the diff algorithm in use to enhance your merges

#### How does code get to production

Based on what explained, we assume that upstream/dev is the common development branch, unless otherwise specified within the team.
The team leader or at least 2 developers can, when everything is stable in upstream/dev, create a pull request from upstream/dev into upstream/master (default release) and create a hard tag release via [console](https://git-scm.com/book/en/v2/Git-Basics-Tagging) or through the github interface.

Once the release is tagged that code will be deployed. This brings two major benefits:

1. you are always aware of the last release tagged and therefore the last version of code deployed
2. it is possible to create dedicated fix branches to address specific release issues

Ideally the tagging would be an automated procedure from the deployment system. In mature CI/CD environments you can revert to soft tags or tagging only major releases.

#### I'm in a forest, there are branches everywhere! Save me!

This development method like others based on feature branching will create many branches locally and in your origin.
Sooner or later you'll have to spend some time to prune your environment. It's important to remember that it is safe to delete branches *ONLY* after they have been merged or when they are no longer needed. They are safe to be deleted after they have been merged through a pull request.

Let's have another quick example on how to keep your environment clean:

```
# list you local branches
git branch

# delete one or more local branches
git branch -D <branc_name1> <branc_name2>

# delete a remote branch from origin
git push origin :<branch_name>

# sync your git objects database references with all your remotes (upstream too)
git fetch --all --prune

# same thing but for a specific remote only
git remote prune origin

# verify the connectivity and validity of the objects in the database
git fsck

# objects database cleanup and compression, this will also speed up git operations overall
git gc

```

It's possible to script most of them together and get a quite complete _git_ cleanup script.
