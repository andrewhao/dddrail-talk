DDD-rail your Monorail 
----------------------

Applying DDD principles to your rails monolith.

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

#### Terms used in the business:

* A **Driver** __picks up__ a **Passenger**
* A **Passenger** __requests__ a **pickup**.
* A **Vehicle** is __driven__ by an **Owner**
* An **Invoice** is __delivered__ to the **Passenger**'s account
* A **User** __logs in__ to the mobile app and changes her password.
* A **Customer** __orders__ an item from a **Restaurant** to be
  __delivered__ by the **Delivery Agent**

### Bounded Contexts and Subdomains

#### Core Domain: what a business does that makes it unique and valuable

For example, Delorean's core domain is "trips"

#### Subdomain: smaller, divided areas of the business with the same logical consistency.

Within Delorean's core domain of "trips", you have subdomains of "ridesharing", "financial transactions", "identity and access management", "food delivery"

#### An aside: linguistic drivers:

The team realized that they had pretty weak vocabulary! In fact, one of
the bugs they've chased for so long was a bug relating to the fact that
an engineer misunderstood "order" for trip payments instead of "order".

##### For example:

- User
  - is actually Passenger (in Trip subdomain)
  - is actually Driver (in Trip subdomain)
  - is actually DeliveryAgent (in Delivery subdomain)
  - remains in Identity subdomain

- Order
  - As it turns out, the DeloreanEATS product owner only ever calls it the Order.
  - the rideshare context calls it a Pickup
  - don't try to mash the two together. They should be separate.

- Vehicle
  - Instead of a user, it has an owner. or should it be termed an
    operator?
  - Is a vehicle driven? Or is it operated?

- Key:
  - Talk to people in your domain. Look for the experts. They will
    surface the right terms for you. You will drive out good
    conversations about the business, too.

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
* Rails-ERD is a gem that prints UML diagram.
* Draw it out in Visio. Or Powerpoint.
* PlantUML
Print out a list of classes and put it all on cards.

Print this out. Don't just keep it virtual.

You will have a conversation. Keep it open for change.

### Step 2: Talk about it.

Bring in people from each of your core business units, or schedule time
with them on their calendars. Have them look at the domain models. Do
they agree with the names, or core interactions?

Develop a __ubiquitous language__, codify it in your __glossary__.

### Step 3: Make a Context Map

Draw lines around your physical system boundaries.
Draw upstream/downstream relationships between them.
These will help you identify dependencies between software systems.

Now we set a goal for ourselves: we want to make software boundaries around each of these language domains. Most likely, our actual software artifact is the Rails monolith, encompassing all the domains.

## Refactoring steps

### Step 1: Rename objects in your software to map to real domain names

Change variable names as necessary. 

### Step 2: Modulize and namespace your objects, files.

Move items into their own namespaces. Suggest using an `app/domains/xyz` folder structure, but can be flexible.

What to do with shared models, entities? Perhaps they can move into their own shared namespace; `app/domains/company_name`. That may be able to move into a gem later in the future.

### Step 3: Use aggregate roots

Identify your aggregates, and enforce that external callers may only use the root object.

Your queries should only return these roots.

### Step 4: Break database joins between domains

Prevent `belongs_to` associations & friends. Instead, take a refactor step where you move that association into a Finder object. Use that Finder object as an anti-corruption layer, translating domain concepts from external domain to your domain.

### Step 5: Rinse, repeat

* This should (and can) be done iteratively!
* Use a business driver to slowly introduce these changes.
* Continual conversations with business domain experts.

## Toward the future

* You can eventually move to microservices, but before that,
* modules -> Rails engines?
* See Component-Based Rails Applications, by Stephan Hagemann of Pivotal Labs.
* Event sourcing, message queues, CQRS, advanced topics for decoupling!

### Misc takeaways

* Your database is NOT your model.
* AR pitfall - not everything needs to be an AR object. Think VERY HARD about whether something needs to be persisted in the table.
* Maybe you can build models on the fly. Or from other data sources.
* app/models can contain non-AR objects!
