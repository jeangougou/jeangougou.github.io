---
layout: post
title:  "repository pattern and the clerk metaphor"
date:   2016-02-01 10:10:21 +0000
categories: architecture design repository
description: "repository pattern most common mistakes"
published: true
tags:
-architecture
-design pattern
-repository
---

## What is a Repository Pattern ?

There’s a lot of misconception around about the Repository Pattern should work. Some even due to a misunderstanding of the pattern itself. As [PoEAA Repository][PoEAA] describes it, the Repository is a pattern meant to:

> Mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects.

Still not clear ?
The pattern is an abstraction to access and persist information. It offloads the responsibility to the underlying storage mechanism while remaining independent from it. It’s storage agnostic, but marks a clear boundary on what’s allowed in and out. Establishes clear contracts with the rest of the codebase.

#### How to dig deeper

There are many interesting and complete links available with thorough explanation of all the nooks and cranny of the pattern. We can read [Microsoft's Repository Pattern][Microsoft's Repository Pattern] description. Otherwise q&a sessions at [StackOverflow's Repository Pattern][StackOverflow's Repository Pattern].

#### What are the most commong mistakes ?

##### Mistake 1: Leaking persistence specific information is the most common mistake.

It decomposes into two specific elements.
First a complete misunderstanding of the pattern. The abstraction layer ends up adding complexity to the software. Second a side effect of increasing responsibility (lacking separation of concerns).

##### Mistake 2: Writing a single storage implementation.

I advocate for consistently testing, developing and maintaining implementations at least two different persistence technologies. This will force you to write better unit tests and confirm the assumptions and the guarantees of the pattern. For instance, a quick “In Memory” implementation for an integration test run. Save a persistence technology failure, the software is tested.

#### The clerk metaphor

I will explain why a clerk is a good metaphor for the Repository pattern.

Let's assume that I have a package (data entities) and that I want that stored for future use. There are 2 simple use cases to address: store said package safely, retrieve it at any time.

I need to go and talk to warehouse inc.'s clerk (our repository pattern) for each one of those cases.

Mapped to the use case of storing a package is the ability to call a save method that persists the data somewhere. Does not need knowledge on where the warehouse is (storage independent).

Mapped to the use case of retrieving at any time there is the ability to call a query method by reference code. Or rather asking a clerk to retrieve you the package you stored a long time ago by showing the receipt.

Your repository pattern is a clerk.  
Your storage is the warehouse.  
Your packages are data entities.  

Have fun!

[PoEAA Repository]: http://www.martinfowler.com/eaaCatalog/repository.html
[Microsoft's Repository Pattern]:https://msdn.microsoft.com/en-us/library/ff649690.aspx
[StackOverflow's Repository Pattern]:http://stackoverflow.com/questions/tagged/repository-pattern
