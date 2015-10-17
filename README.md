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


### Hi, I'm Andrew

I work at Carbon Five. I used to come from a company called Blurb, where
I derived some semblance of truth from these stories.

### Show of hands

How many of you use Rails?

How many of you are on a monolith?

How many of you are familiar with DDD?

### Delorean: It's like Uber for Time Travel!

- Motivation: why break it apart
- hard to book things
- Growth

### The scenario: OMG Monolith!

Engineering team is sad. demoralized. hard to ship. people leaving.

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
  - Vehicle
- Notes:
  - A user can request a trip
  - A driver can fulfill a trip
  - The user pays the driver a fee

### Next: Let's let users book rides for different vehicle classes!

- UML (2-ride-tiers)
  - Trip
  - User
  - Payment
  - Vehicle
  - ServiceTier
- Notes:
  - A user can select what type of car they want to have, which has
    its own pricing strategy
  - Add complexity in order calculation (Payment) engine. Maybe you
    thought you could manage this extra cost calculation in your
controller.
  - The business capability of charging somethign different for a
    different class of service is enscapulated in `ServiceTier`

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

### Meanwhile, Delorean grew.

  - What was once now one team is now three teams: trips, eats, and
e-commerce. and mobile.

### Some hiccups happened...

  - TripRouterService is flooding the DB with queries and generating
    high load on server nodes.
  - Payments team introduces regression in Trip while refactoring some
    ServiceTier prices.
  - Eats team deploys new data migrations to MenuItems, but forget to
    recalculate them with new InflationAdjustments
  - Mobile API developers request a new mobile login flow, but which
    team should implement it?
  - Somebody tries to implement Order by making it polymorphic STI... uh
    oh.

### And the business kept wanting to do new things.

  - Outsourced dev shop is rebuilding the marketing home page and needs
    a pricing API.
  - CEO asks: what if we also added "Airbnb for time travel - host travelers from
    other decades"? Let's launch that in 6 months!

### Hold up!

  - Developers are frustrated
  - Can't find new 

### What did the team do?

They pulled out a copy of "Implementing Domain-Driven Design" by Vaughan
Vernon and followed it closely.

### Principle 1: Ubiquitous Language

The team sits down and realizes they need a good understanding of the
business at large. In fact, they're surprised to discover they've never
really sat down and talked about things and their meanings.

### Bounded Contexts and Subdomains

#### Core Domain: what a business does that makes it unique and valuable

For example, Delorean's core domain is "trips"

#### Subdomain: smaller, divided areas of the business with the same logical consistency.

Within Delorean's core domain of "trips", you have subdomains of "trip
planning", "ordering", "routing", "deliveries"

#### An aside: linguistic drivers:

The team realized that they had pretty weak vocabulary! In fact, one of
the bugs they've chased for so long was a bug relating to the fact that
an engineer misunderstood "order" for trip payments instead of "order".

For example:

- User
  - is actually Passenger (in Trip subdomain)
  - is actually Driver (in Trip subdomain)
  - is actually PurchaseAgent (in Delivery subdomain)
  - remains in Identity subdomain

- Order
  - As it turns out, the DeloreanEATS product owner only ever calls it the Order.
  - the delivery context calls it a Booking.
  - don't try to mash the two together. They should be separate.

- Key:
  - Talk to people in your domain. Look for the experts. They will
    surface the right terms for you.

#### Bounded context:

The limited applicability of a certain term.
This is actually also modeling the *solution space* - that is, your
software system should only ever enscapulate the linguistic driver.

### Ubiquitous language:

The phrase "The driver makes a delivery" has different meanings in
different contexts. A delivery could be a food order in the Delivery
context, or it could be a human ride-along trip in the Rides context.

### Step 1: Visualize it.

* Find a diagram.
* Railroady is a gem that prints UML diagram.
* Draw it out in Visio. Or Powerpoint.
* PlantUML
Print out a list of classes and put it all on cards.

Print this out. Don't just keep it virtual.

You will have a conversation. Keep it open for change.

### Step 2: Talk about it.

Bring in people from each of your core business units, or schedule time
with them on their calendars. Have them look at the domain models. Do
they agree with the names, or core interactions?

### Misc takeaways

Your database is NOT your model.

AR pitfall - not everything needs to be an AR object. Think VERY HARD about whether something needs to be persisted in the table.
Maybe you can build models on the fly. Or from other data sources.
app/models can contain non-AR objects!
