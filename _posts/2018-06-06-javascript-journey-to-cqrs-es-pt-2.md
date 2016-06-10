---
layout: post
title:  "JavaScript journey to CQRS-ES in JS - pt2"
date:   2018-06-06 18:49:23 +0000
categories: programming
description: "evolution from classical N-tier development architectures to CQRS, and subsequently Event sourcing with sample JavaScript implementation; part 2"
published: false
tags:
- CQRS
- JavaScript
- event sourcing
- MEAN
---

Now we can concentrate on moving the persistence of the todo list from a page-bound local storage element into mongoDb.  
Replicating the same infrastructure is the key first step.
