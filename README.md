DDD-rail your Monorail 
----------------------

Applying DDD principles to your rails monolith.

## Welcome

* In a prior life, I led a team
* B---- was a self-publishing platform that helped authors create,
  publish, and sell books & ebooks.
* Monolithic app
* Problems: hard to release, regressions, spaghetti code, turnover, team
  communication was hard.

## What to do?

* I hear services are great. But where do we start?
* Lead tells me: read DDD.

## Summary

* mapped the problem
* implemented a new feature with DDD and did an incremental refactoring
* future steps

## Step 1: Map the problem

* Existing system: UML diagram
* Start with railroady: bottom-up diagram.
* Domain map overlay

* Define terms:
  - Bounded context
  - Domain
  - Subdomain

* Principle: Find domain expert. Get language right. Ubiquitous
  language.

## Step 2: Using a biz driver, implement with DDD tactics

* Amazon: let's sell books on Amazon
* Special configuration options, new pricing schemes/requirements
* Two ordering systems: Amazon + ours
* Integration with MWS marketplace fulfillment service

* Split out models
* Use value objects
* Move data relationships around
* Rule: no direct joining into this subdomain. AR abuse? No!
* namespace the domain
* app/services/amazon/...
* Aggregate root: only get to value objects through the AmazonOrder.

## Step 3: Driving toward services - the future

* Rails engines
  - enforce good boundaries
  - no joins
  - shared kernel: gem-ify your core domain objects.

* Domain events
  - notification vs publishing domain events

  - simple Ruby domain events
  - as you extract services: use pub/sub patterns in code: integration with -MQ or Redis.

### Scratch thoughts

- DDD encourages Translator objects - may not need to be so heavyweight
  in your Rails apps. The idea is there though - you use an Adapter to
  fetch from remote domain, then use Translator to translate that domain
  language to your own.
- Pure DDD encourages you have Service objects in your own domain that
  talk to remote Adapters that implement HTTP facades. Kind of heavy.
  (Figure 3.10) These are referred to as "boundary objects"

### Further Reading

- "Implementing Domain-Driven Design" by Vaughan Vernon
- https://developers.soundcloud.com/blog/building-products-at-soundcloud-part-1-dealing-with-the-monolith

### Scratch

book
amazon book
contrived example

"i can't see what you're talking about"
domain driven design

**what did you actually do?**

ubiquitous language
extract core example

actual techniques
* how did we deal with migrations
* risks: how to roll out

how did we share models

rails engines

start breaking it up

how to handle transactional data

how to apply DDD to rails

* aggregate root

show us the real thing

"how do i do that?!"

"what were the migrations like"

context map:
  draw it out
  draw directionality of relationships

examples of book model to the end

- core domain models
- extracting value objects
- aggregate root


