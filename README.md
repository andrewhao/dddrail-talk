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

## Examples

### Scenario 1:

Book scenario domains:

- Creation
- Printing
- Shipping
- Ordering
- Selling

### Scenario 2:

Airline booking system:

- Flight
- Seat
- SeatOpening
- User
- Booking
- Passenger

Domains:

- Booking
- Purchasing
- Routing

### Delorean: It's like Uber for Time Travel!

- Motivation: why break it apart
- hard to book things
- Growth

## OMG Monolith!

### What is a monolith?

- You're may be on a monolith if:
  - tables everywhere
  - hard to deploy
  - you find it hard to scale
  - regression err-where
  - your Rails app has been in production for over three years.
- Wait! Monoliths aren't bad! Don't beat yourself up!

### Moving on, let's join our heroes in their story.

- Delorean is Uber for time travel.
- Diagram: human, delorean, timeline (1985, 1955, 2015)

### In the beginning, all was peachy.

- UML (1-basic)
  - Trip
  - User
  - Payment
- Notes:
  - A user can request a trip
  - A driver can fulfill a trip
  - The user pays the driver a fee

### Next: Let's let users book rides for different vehicles!

- UML (2-ride-tiers)
  - Trip
  - User
  - Payment
  - Vehicle
- Notes:
  - A user can select what type of car they want to have, which has
    its own pricing strategy
  - Add complexity in order calculation (Payment) engine. Maybe you
    thought you could manage this extra cost calculation in your
controller.

### Next: Time travel carpooling!

- Notes:
  - Oh boy! We can charge people a lower rate if we can coalesce two
    trip members into one trip.
  - Hmm, we have to add custom rates for this trip pool
  - And we have to model the pool
  - And we have to now calculate the complexities of this trip
    (PaymentCalculatorService)

### Next: DeloreanEats - time travel food delivery

How about we have the driver fetch you some food from your favorite restaurant from the past?

- Notes:
  - Uh oh, I've gotta calculate and adjust for inflation! How do i bill for something in the past, paid in the present?
  - We chose "order", but shoot, that's kind of generic. Did we name
    this wrong?
  - Hmm, now Payment can refer to either a trip or an Eats order.
    Either may be nil.
