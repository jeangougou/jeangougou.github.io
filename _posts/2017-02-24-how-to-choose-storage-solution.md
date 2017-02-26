---
layout: post
title:  "How to choose a storage solution"
date:   2017-02-24 23:51:21 +0000
categories: architecture storage infrastructure
description: "RDBMS, document, key-value, columns and tables. How to pick the right database type for your needs"
published: true
tags:
- database
---

It's been a while since my last post and life has been gracious enough to allow me to take time and write again.

This article spawns from an interesting point of view I head recently. 

_I want a DB with native RESTful APIs on top_

This was a much surprising statement, I really did not expect anyone throwing something like that at me.
I took my time to digest that but I still can't see the value of picking the right storage solution for your needs based on an accessory.
It's like choosing what car you'll be driving based solely on the width of the tires.

Now, obviously, the width of the tires can be an important factor if you drive sports car or go off-road quite a bit, but ultimately it's unlikely it's going to be your deciding factor for the purchase.
Similarly the presence of a layer of RESTful APIs can't be the single element to factor in when assessing your DB choice.

## What do I have available in terms of storage?

The simplest solution in terms of storage is usually a file based storage, you can save data into your local hard drive, on a NAS, or the equivalent cloud options (S3, Google Drive and many more).
Let's concentrate on Databases this time around.

There are many types of databases, each one specifically designed for a (set of) purpose(s).
I have to say that for small applications most choices will still be correct as they can cover pretty well all the use cases.
Using a database model to solve a problem that's not really mean for it can feel like trying to push a nail on a wall with a pair of scissors.
Eventually you'll make it but it not going to be pleasant.

### How many database models are there?

That's an interesting question, if we consider that many DB engine available can support multiple models too.
[Wikipedia to the rescue](https://en.wikipedia.org/wiki/Database_model)!
There are many database models but we can probably classify the main ones in use today as one of the following:
* Relational
* document
* Key-value
* Object
* Graph

This excludes the list of old models before [Ted Codd](https://en.wikipedia.org/wiki/Edgar_F._Codd) proposed the Relational Model, helped by [Ray Boyce](https://en.wikipedia.org/wiki/Raymond_F._Boyce) to formalize any internal data relation into it's minimal and most optimized form. Back in the '70s hard disk space was an issue.
We will skip highly specialized database models for now (time series, event store, xml and so on)

### Why should I pick a model over another?

Some tools are built to solve a certain set of problems. I'm not saying you won't be able to write your application if you use a certain Database model as storage instead of another. 
I'm saying that if you want to have an easier development, less error prone, which also happens to run faster (maximize efficiency) you can try and find a better suited solution for what you want to store.
Keep in mind that you can use multiple storage mechanisms, this solves a wide arrange of problems and introduces one of the most complicated ones: consistency. But that's a topic for another day.

### Database models

The purists will blame me for not separating the storage model with the database model; the reason I do this is because if you look for a DB to employ in your next application you will find extremely confusing statements around. With very few exceptions a Graph database will tell you that it's using a document store rather than a relational type store inside. You'll have to figure it out yourself or reading the docs very accurately. It's safe to assume that, independently from the underlying storage engine adopted, the Database will provide you with a fair usage of the model it's trying to represent.

#### Object

It's hard to find a pure object model in the wild nowadays. The main purpose of this model is to keep containers (vector, arrays, sets, lists), nested objects and custom datatypes.
They are meant to hold what's defined in code with a matching storage model that will safely allow things to get in and out.
If you have used an ORM library before, you already went through the process of converting to this data model, but probably stored the information in a pure Relational database.
ORM stands for Object-Relational mapping.
This model works best when you have a complex set of data and hard performance requirement.

#### Graph

Based on graph theory, they adopt a model composed of 3 major elements: Nodes, edges and properties.
Nodes are the main entities, edges are the arcs connecting those entities while properties describe attributes of the nodes.
This is a particularly fit model to store highly interconnected data and for queries that go more than one level deep.

#### Key-value

Hard to define as there are very complex underlying assumptions differing from one commercial implementation to the next. Some believe on immediate consistency, some leave everything in RAM.
The data storage paradigm is fairly simple, the DB can be abstracted as an highly performing dictionary. Some Graph databases use this internally to store data.
I'd say it's a good choice when experimenting and we don't care about what we are storing. A very apt choice for any sort of caching system.
It mostly depends on which KV store it is and its specific implementation.

#### Document

Everyone seems to love document databases, effectively they are a subclass of Key-values ones adding properties to the value stored so it can be retrieved with its metadata.
Its main difference with the classical relational model is the integrity of the concept to model, while in a relational model an object can be spread across multiple tables in a document model that's unlikely to happen.
A very common misconception is the equality of unstructured data = restful api. This makes little sense in general as the ESB will save every enterprise from its internal architectural issues.
The fact that a document model is extremely fit to store unstructured data, for instance when a document contains not only different data but also a different structure for the data, doesn't mean it's a good fit for anything in particular.
It's definitely beneficial in any initial stage of a RAD prototype.

#### Relational

Between the '70s to early 2010s this has been the de facto choice for any storage need.
If you can easily model your current data in a structured format and your use case queries don't go more than one level deep this is a great choice for you.
They work relatively well if used as Key-Value stores, comparably with the ones that don't run in-memory (RAM is inherently faster than disk access).

## Conclusion

Read it again and sum up your own conclusions, I did my part of the work already. Just kidding.

Assess if your data is structured or unstructured, if you need to write complex queries joining data from multiple places, if you need consistency or volatile data is good enough.
Depending on your needs there are several good options out there, sometimes preventing adoption might even be a lack of a library for your current stack.
