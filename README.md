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

## Examples

### Scenario 1:

Book scenario domains:

- Creation
- Printing
- Shipping
- Ordering
- Selling

