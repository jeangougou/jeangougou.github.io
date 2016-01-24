---
published: false
---

Other ideas to elaborate on DDD/CQRS/ES:

Every question will not translate anymore in a SQL-like query, neither any other declarative language nor any immediate intelligibility.
It will be much harder to analyze an event stream rather than simply querying a key-value database, and this will translate in a lot of clever code in many different places that is hard to maintain and requires a solid understanding of the basics.
Event Sourcing code is generally more complex and requires following a set of rules which are very hard to enforce in automated fashion.

ES requires careful event design and careful aggregate root classes implementation. If at any given point the aggregate root enters in a valid state there is no possibility of "updating" the current last available status quickly. Additionally any change to the set of events available and the workflows and processing handling those events has to be carefully evaluated (ideally through automated tests).

* We should always add the Aggregate Root unique identifier to each and every event or even the smartest system wouldn't be able to apply it to the right elements.

* Event Sourcing can answer many questions but makes phrasing said questions a lot harder (Douglas Adams' "Hitchhiker's guides to the Galaxy" anyone?)

* Event Sourcing is effectively more error prone for beginners

* Repairing an event stream gone bad that lead to an invalid state for the Aggregate is particularly challenging