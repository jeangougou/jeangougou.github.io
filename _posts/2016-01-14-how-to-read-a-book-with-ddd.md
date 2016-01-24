---
layout: post
title:  "How to read a book with DDD"
date:   2016-01-14 22:51:21 +0000
categories: architecture design
description: "differences between a vanilla Domain Driven Design (DDD) and Event Sourcing (ES) from a persistence layer perspective"
published: true
---

### Wait, what's DDD again ?

This article requires familiarity with DDD concepts. 

Let's start with defining what DDD (domain driven design) is not:

* DDD is not a method
* DDD is not an architecture
* DDD is not a silver bullet

DDD manifests as a strategic approach towards the simplification of software development. It can achieve that by reducing complexity and dividing responsibility while employing strategic assets.
To mention some: Ubiquitous Language, Aggregates, Domain Events, Bounded Contexts and so on.

If these concepts are not clear at all, the rest of the article will not make much sense.


### DDD in different flavors

Having read Evans' and Vernon's books you are already familiar with Event Sourcing (henceforth ES). There are many advantages and many disadvantages as well. The increasing complexity of code can be beyond the requirements of a project.

Let's see in what flavors we can have plain old DDD and compare it with ES following a simple example.

Our progress in reading a book is reflected in the Bounded context, and more in general in the Domain.
It is a simplified example for the domain but it will be easy to grasp from a workflow perspective.

### How to read a book with DDD

We can define limited scopes, use case scenarios and bounded contexts in a vanilla DDD approach. To ease the example let's consider the three following scenarios:

I can move to next page.
I can move to previous page.
I can move to page X, where X can be any valid page number.


a sample code snippet that should be able to handle that for me would look as in:

{% highlight javascript %}

function moveToNextPage(){ ... };

function moveToPreviousPage(){ ... };

function moveToPage(pageNumber){ ... };

{% endhighlight %}

We expect to be able to read, at any point, the page we are currently at. 
The current page number can thus be any positive integer value between one and the total length of the book.
So a short _Aggregate Root_ for the book entity would contain a constructor enforcing this case.

{% highlight javascript %}

class BookAggregate 
{
  // a constructor shall always enforce a valid state, since the inception of a new instance
  // having the id generated as soon as possible can help a long way in a context of eventual consistency
  BookAggregate(id, totalNumberOfPagesInTheBook){
    this.id = id;
    this.currentPage = 1;
    if(totalNumberOfPagesInTheBook <= 0)
     throw new Error("Uhm .. you should count the book pages again.");
    this.totalNumberOfPagesInTheBook = totalNumberOfPagesInTheBook;
  }

  // meant to be private
  function isValidPage(nextPage){ 
    return 1 <= nextPage && nextPage >= this.totalNumberOfPagesInTheBook;
  }
  
  // simple getter, also private
  function getCurrentPage(){
    return this.currentPage;
  }
}

{% endhighlight %}

We already have a book representation with an identifier, a current page and the total number of pages element. Also a validating method if the next page we want to jump to exists in the book.

Now we can consider the most complex scenario: jumping to an arbitrary page in the book.
The implementation can be quite straightforward given what we have already seen in the class snippet.

{% highlight javascript %}

function moveToPage(pageNumber){ 
  // check if the page is in the book
  if(!isValidPage(pageNumber))
  {
    throw new Error(
      "Oh snap! the book only contains: "
      + this.totalNumberOfPagesInTheBook 
      + " and I can't really select page " 
      + pageNumber);
  }
  this.currentPage = 1;
  ... 
  // domain event publishing
  ...
};

{% endhighlight %}


It is expected to validate the data coming in into the _Aggregate_. If a book contains only 105 pages, it won't be possible to read page 108. Before completing the operation we need a check or it would move the _Aggregate_ into an invalid state.

Other two methods, to move to a single page forward or backwards, can be implemented by wrapping the _moveToPage_ method.

It is generally better to use self encapsulation whenever possible. This is a perfect scenario for this because it's easy to abstract the next page number we want to focus on. This way we also reduce risks of having side effects.

Using direct access to fields can void the benefits of self-encapsulation. By managing an _Aggregate_ in a single file we have less code to review and thus, this approach is less error prone. The last two methods can be quickly implemented as:

{% highlight javascript %}

function moveToNextPage(){ 
  moveToPage(getCurrentPage() + 1);
};

function moveToPreviousPage(){ 
  moveToPage(getCurrentPage() - 1);
};

{% endhighlight %}

### What book am I reading again?

Now we have a simple model on how to read a book and track our progress while reading it. We can move one page to the next, or skip forward, or even go back a chapter. When does *Event Sourcing* starts being different then?

The answer is simple in theory (only). A vanilla DDD implementation saves exclusively the last valid state of the _Aggregate_. Last valid state is computed by applying all the previous events registered. In *Event Sourcing* instead we save all those events that lead to the incremental evolution of the _Aggregate_. In *Event Sourcing* then, we can read all events but one an get, for instance, the previous valid state. In a vanilla DDD implementation that would mean saving all the previous states separately. 


{% highlight javascript %}

// current book aggregate
var isbnCode = '1234-0987';
var pagesInTheBook = 345;
var book = commandHandler.CreateBook(isbnCode, pagesInTheBook);
repository.save(book);

// storage will contain then
=> 
{
  id: "1234-0987",
  currentPage: 1,
  pagesInTheBook : 345
}

{% endhighlight %}

By starting to read the book and persisting it to the repository over and over, with operations like:

{% highlight javascript %}

var isbnCode = '1234-0987';
var book = repository.get(isbnCode);

commandHandler.moveToPage(3);
repository.save(book);

commandHandler.moveToPage(4);
repository.save(book);

// storage will now contain
=> 
{
  id: "1234-0987",
  currentPage: 4,
  pagesInTheBook : 345
}

{% endhighlight %}

Clearly the current page number is the last one persisted through the repository (i.e. 4). And in no case we can be sure of how we got there, the sample shown up until now, in fact, has skipped page 2. 

Naturally, we can add more code and devise a neat strategy to address that issue. 
We could start tracking the previous page and add a comparison step; if the distance is more than one page, we are heading too far. Or  we could add a completely separate page tracking mechanism to show which pages we've already visited.

Or again, we could move to *Event Sourcing* and start saving every simple valid operation that has happened:

{% highlight javascript %}

BookAggregate(id, totalNumberOfPagesInTheBook){ // so by default 
    ...
    // on creation we want to know that a new book is available from an event perspective too
    publish({
      bookIsbnCode: this.id,
      eventName:"BookAdded",
      totalNumberOfPagesInTheBook: totalNumberOfPagesInTheBook,
      pageNumber : this.currentPage, // we assumed before that the initial page is page 1
      when: new Date().toISOString()
    });
  }

...

function moveToPage(pageNumber){ 
  // previous implementation
  ...
  // publishing the event 'MovedToPage' to track the page change to the parameter pageNumber
  publish({
    bookIsbnCode: this.id,
    eventName:"MovedToPage",
    pageNumber: pageNumber,
    when: new Date().toISOString()
  });
};

{% endhighlight %}

We have now saved two simple events. They represent the whole composable status of the current _BookAggregate_ instance. On top of that we have all the historical audit trail that lead to the current status.

What would these events look like then? Here's a first guess:

{% highlight javascript %}

{
  bookIsbnCode: "1234-0987",
  eventName:"BookAdded",
  totalNumberOfPagesInTheBook: 345,
  pageNumber: 1,
  when: "2016-01-01T22:21:40.806Z"
}

// then, moving to page 3
{
  bookIsbnCode: "1234-0987",
  eventName:"MovedToPage",
  pageNumber: 3,
  when: "2016-01-04T10:27:26.942Z"
}

// then again, moving to page 4
{
  bookIsbnCode: "1234-0987",
  eventName:"MovedToPage",
  pageNumber: 4,
  when: "2016-01-06T15:04:34.347Z"
}

{% endhighlight %}

Given these simple set of events it's easy to figure out if page was skipped. 
We could also answer questions like: "what page was I reading on 5th January?", "Had I read page 24 on a given date?" or "How many pages on average do I read in a week?".

This higher flexibility comes at a cost, and it is not cheap.
