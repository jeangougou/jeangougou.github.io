---
layout: post
title:  "How to read a book with DDD"
date:   2016-01-14 22:51:21 +0000
categories: architecture design
description: "differences between a vanilla Domain Driven Desing (DDD) and Event Sourcing (ES) from a persistence layer perspective"
published: false
---

# DDD in different flavors

If you have had the chance of reading Evans or Vernon's books you are probably familiar with Event Sourcing (henceforth ES) and the scenarios when it's worth employing that technique. There are many advantages but it also adds a level of complexity that sometimes is far beyond the scope of a project.

Let's see in what flavors we can have plain old DDD and compare it with ES following a simple example.

We will now highlight the main behavioural differences in a bounded context that's mean to reflect our progress in reading a book. It might be and overly simplified example for the domain but it will be particularly easy to grasp from a workflow perspective.

### How to read a book with DDD

In a vanilla DDD approach, we would have defined the bounded context and some scope limited use case scenarios. 
To ease the example let's consider the three following scenarios:

I can move to next page.
I can move to previous page.
I can move to page X, where X can be defined elsewhere.


a sample code snippet that should be able to handle that for me would look along the lines of:

{% highlight javascript %}

function moveToNextPage(){ ... };

function moveToPreviousPage(){ ... };

function moveToPage(pageNumber){ ... };

{% endhighlight %}

Naturally we expect to be able to read, at any point, the page we are curretly at. 
This page will be rapresented by any positive integer value within the interval from 1 to the page lenght of the book.

so a short aggregate root for the book entity would probably contain a constructor enforcing this

{% highlight javascript %}

class BookAggregate 
{
	// a constructor shall always enforce a valid state, since the inception of a new instance
	// having the id generated as soon as possible can help a long way in a context of eventual consistency
	BookAggregate(id, totalNumberOfPagesInTheBook){ // so by default 
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

We already have a book rapresentation, defined at least by an identifier for the book so we can retrieve it any time, a current page and a number of pages. In addition to that a method of validating if the next page we want to jump to really exists in the book.

Now we can consider the most complex scenario, jumping to a generic page in the book and we see that the implementation can be quite straightforward given what we have already seen in the class snippet.

{% highlight javascript %}

function moveToPage(pageNumber){ 
	// check if the page is in the book
	if(!isValidPage(pageNumber))
	{
		throw new Error("Oh snap! the book only contains: "+ this.totalNumberOfPagesInTheBook + " and I can't really select page " + pageNumber);
	}
	this.currentPage = 1;
	... 
	// domain event publishing
	...
};

{% endhighlight %}

Again a direct sample that before moving to the desired page checks again if the action can be done successfully, if the book contains 105 pages and we try to read page 108 there is a problem somewhere.
The other two methods that move a single page forward of backward can be internally implemented through the _moveToPage_ method, it is generally better to use self encapsulation whenever possible, particularly in aggegates as the risk of self encapsulation failing is minimized by the size of the element.
Self encapsulation starts failing only when there is at least one method of accessing the resources without the self encapsulation, therefore in a single file, tendentially small by the very principle guiding its design, we should be relatively safe.
The last two methods can be quickly implemented as:

{% highlight javascript %}

function moveToNextPage(){ 
	moveToPage(getCurrentPage() + 1);
};

function moveToPreviousPage(){ 
	moveToPage(getCurrentPage() - 1);
};

{% endhighlight %}

### What book am I reading again ?

Now we have a simple model that describes how to read a book and can track all our progress from one page to next or, if we leave a long time between reads, how to pick up an old page or even skip forward if it become boring or too obvious.
When does *Event Sourcing* starts being different then ?

The answer is simple, in a vanilla DDD implementaion context every domain event will be applied to the _BookAggregate_ class and a repository will save the whole current state of the book (unique identifier, current page and total number of pages) so that at any time we have access only to the last valid state of the BookAggregate.


{% highlight javascript %}

// current book aggregate
var isbnCode = '1234-0987';
var pagesInTheBook = 345;
var book = commandHandler.CreateBook(isbnCode, pagesInTheBook);
repository.save(book);

// storage will contain then
=> 
{
	id: 1234-0987,
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
	id: 1234-0987,
	currentPage: 4,
	pagesInTheBook : 345
}

{% endhighlight %}

Clearly the current page number is the last one persisted through the repository (i.e. 4). And in no case we can be sure of how we got there, the sample shown up until now, in fact, has skipped page 2. 

Naturally, we can add more code and devise a neat strategy to address that issue. 
Maybe by tracking the previous page too and comparing it to the next page we want to jump too, when the distance between the integer rapresentation of those pages is greater than one we are heading too far.
Maybe a page read system that tracks separately whenever we have accessed a page or not, possibly even when we did it by saving the time.

Or we could use *Event Sourcing* and, instead of saving the whole BookAggregate as its current last status only we can save every single page change by adding 2 different type of events:

{% highlight javascript %}

BookAggregate(id, totalNumberOfPagesInTheBook){ // so by default 
		...
		// on creation we want to know that a new book is available from an event perspective too
		publish({
			bookIsbnCode: this.id,
			eventName:'BookAdded',
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
		eventName:'MovedToPage',
		pageNumber: pageNumber,
		when: new Date().toISOString()
	});
};

{% endhighlight %}

We added 2 simple events, that can represent the whole status of any specific instance of a _BookAggregate_ class, and they will constitute an history of what happened.
Let's assume the same behaviour as before is carried through but, instead of saving the whole current state, at each iteration, we keep track of the events published and through the repository we save only those as a stream of changes, we will end up with something like the following:

{% highlight javascript %}

{
	bookIsbnCode: 1234-0987,
	eventName:'BookAdded',
	totalNumberOfPagesInTheBook: 345,
	pageNumber: 1,
	when: '2016-01-01T22:21:40.806Z'
}

// then, moving to page 3
{
	bookIsbnCode: 1234-0987,
	eventName:'MovedToPage',
	pageNumber: 3,
	when: '2016-01-04T10:27:26.942Z'
}

// then again, moving to page 4
{
	bookIsbnCode: 1234-0987,
	eventName:'MovedToPage',
	pageNumber: 4,
	when: '2016-01-06T15:04:34.347Z'
}

{% endhighlight %}

Given these simple set of events it's easy to figure out if page was skipped. 
We could also answer questions like: "what page was I reading on 5th January ?", "Had I read page 24 on a given date ?" or "How many pages on average do I read in a week ?".

This higher flexibility comes at a cost, and it is not cheap. 
Every question will not translate anymore in a SQL-like query, neither any other declarative language nor any immediate intelligibility.
It will be much harder to analyze an event stream rather than simply querying a key-value database, and this will translate in a lot of clever code in many different places that is hard to maintain and requires a solid understanding of the basics.
Event Sourcing code is generally more complex and requires following a set of rules which are very hard to enforce in automated fashion.

ES requires careful event design and careful aggregate root classes implementation. If at any given point the aggregate root enters in a valid state there is no possibility of "updating" the current last available status quickly. Additionally any change to the set of events available and the worflows and processing handling those events has to be carefully evaluated (ideally through automated tests).

Remember:

* We should always add the Aggregate Root unique identifier to each and every event or even the smartest system wouldn't be able to apply it to the right elements.

* Event Sourcing can answer many questions but makes phrasing said questions a lot harder (Douglas Adams' "Hitchhiker's guides to the Galaxy" anyone ?)

* Event Sourcing is effectively more error prone for beginners

* Repairing an event stream gone bad that lead to an invalid state for the Aggregate is particularly challenging
