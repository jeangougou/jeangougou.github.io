---
layout: post
title:  "CQRS vs MVC"
date:   2016-05-5 23:51:21 +0000
categories: architecture
description: "Differences and similarities between CQRS and MVC"
published: true
tags:
- ddd
- CQRS
- mvc
---

The complete title should be a fair confrontation between a pure MVC only approach and the benefits we can gain by pairing it up with CQRS. As this might have hinted already, they can live together, and in most implementations around the actually do.

If we compare the pure approach on both there are some major differences, as it's natural to expect.
Let's take a step back first and get our head around what are the most common design patterns currently.

### MVC

Model, View and Controller. Each of them with their separate responsibility. The _user_ will see a *View* and trigger an action that will be handled by the *Controller* thus updating a *Model* (which is our data representation). Once the *Model* has been updated any subsequent load of the *View* will reflect those changes.

### MVC alternatives

There are two mainstream alternatives to _MVC_ that can be adopted to achieve similar results. Most of the times the real difference is based on the preferred technology support or framework available that already favors one over the other, rather than a real architectural choice. These alternatives are _MVVM_ and _MVP_ (not the Minimum Viable Product).
Each has 3 main components.

_MVVM_ is a composition of *Model*, *View* and *ViewModel* (following the ancient naming tradition of "let's just keep reusing the same nouns to make it more confusing"). Both *Model* and *View* are the same, the *ViewModel* serves a slightly different purpose from the *Controller* in _MVC_. More in detail, while _MVC_'s *Controller* acts as a man in the middle to coordinate the other components, _MVVM_'s *ViewModel* is connected with a 2-way data-binding to one or more views; this component represents the state of data in the *Model*.

_MVP_, where the P is the *Presenter*, which can be a lose form of the *Controller*. Sometimes also a supervising controller is present to handle the events coming from the view.

### In case CQRS is forgotten

If _CQRS_ as a term is unfamiliar at all, I suggest the very complete [Udi Dahan's explanation][clarified-cqrs], otherwise is an architectural approach, not a patterns per se. Alternatively _CQRS_ can be looked at the architectural equivalent of the [_CQS_ principle][cqs-wiki] for imperative programming.

### Similarities between MVC and CQRS

_CQRS_ can be seen as a precise extension of the _MVC_ pattern, as it advocates for having a very similar split of responsibility. Let's assume we have 2 different _MVC_ models: MVC-Read and MVC-Write. There is no distinction from the views of either, models would be different from each other and the controllers are split over the responsibility of reading and writing. In a pure RESTful implementation the read-controller shall contain only GET methods, while the write-controller shall contain everything but GET methods ( i.e. POST, PUT, DELETE). The only component missing is one that reads from the write-model, aggregates information if needed, and pushes it to the read-model for availability. This would be a good sample to describe the inner workings of a _CQRS_ approach, after all [Greg Young said it first][GY-cqrs-and-mvc].

An additional hint in favor of this same argument is the fact that it's easy to find numerous ASP.NET MVC implementation of _CQRS_.

### Differences between MVC and CQRS

The caveat here is assuming that we are referring to common implementation of pure _MVC_ and _CQRS_ implementation.
The main difference I would point out is the execution time of the 2, _MVC_ would generally act as a synchronous application while _CQRS_ would act asynchronously. Moreover we are entering the painful realm of [Eventual Consistency][Eventual_consistency]. Why painful you may ask ? Because if you study the [CAP theorem][CAP_theorem] you also realize that Consistency is a lot more un-eventual that one might expect it to be.

Regardless of the implementation, another difference is related to the amount of components. _CQRS_ will have more components than _MVC_.

_CQRS_ allows for higher theoretical scalability. This is not a feature by itself, it can achieve it by increasing software complexity and making debug more difficult, therefore it is not a valid reason to favor it over other options if scaling is not an issue with other implementations.

### Conclusions

_MVC_ and _CQRS_ can work together, or you can pick one over the other depending on your requirements. CQRS will add complexity to the solution by adding more components and changes of expected behavior (see [Eventual Consistency][Eventual_consistency]). Don't pick _CQRS_ over _MVC_ for a simple application, it's probably not worth the effort.


[Channel9 (MVC,MVP and MVVM)]:https://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DPR305
[Gui architectures]:http://martinfowler.com/eaaDev/uiArchs.html
[Understanding MVC, MVP and MVVM Design Patterns]: http://www.dotnet-tricks.com/Tutorial/designpatterns/2FMM060314-Understanding-MVC,-MVP-and-MVVM-Design-Patterns.html
[MVC vs. MVP vs. MVVM]:https://nirajrules.wordpress.com/2009/07/18/mvc-vs-mvp-vs-mvvm/
[clarified-cqrs]:http://udidahan.com/2009/12/09/clarified-cqrs/
[cqs-wiki]:https://en.wikipedia.org/wiki/Command%E2%80%93query_separation
[GY-cqrs-and-mvc]:http://www.codebetter.com/gregyoung/2010/09/07/cqrs-and-mvc/
[Eventual_consistency]:https://en.wikipedia.org/wiki/Eventual_consistency
[CAP_theorem]:https://en.wikipedia.org/wiki/CAP_theorem
