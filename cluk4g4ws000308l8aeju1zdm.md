---
title: "The one with highlights of the red book of DDD"
seoDescription: "Highlights of the red book of DDD (Implementing Domain-Driven Design) written by Vaughn Vernon"
datePublished: Wed Apr 03 2024 18:08:30 GMT+0000 (Coordinated Universal Time)
cuid: cluk4g4ws000308l8aeju1zdm
slug: the-one-with-highlights-of-the-red-book-of-ddd
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1711909751649/68a28efd-6897-4c32-8c15-74b27f252062.jpeg
tags: software, software-development, software-architecture, software-engineering, domain, ddd, domain-driven-design

---

Today I'll highlight my favorite book about *Domain-Driven Design* (DDD) so far, written by Vaughn Vernon more than 10 years ago: [Implementing Domain-Driven Design](https://www.amazon.es/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577).

---

## 1. Getting started with DDD

The software development approach called *Domain-Driven Design* (DDD), exists to help us more readily succeed at achieving high-quality software model designs.

Domain experts don't know everything about their business, and they are also going to learn more about it.

A **domain model** is a software model of the very specific business domain you are working in. Often it's implemented as an object model, where those objects have both data and behavior with literal and accurate business meaning.

### Why you should do DDD

There are zero translations between the domain experts, the software developers, and the software [...] because your team develops a common, shared language that everyone on the team speaks.

**Strategic design** helps us understand what are the most important software investments to make, what existing software assets to leverage in order to get there fastest and safest, and who must be involved.

**Tactical design** helps us craft the single elegant model of a solution using time-tested, proven software building blocks.

DDD has some up-front cost of time and effort for the team.

Use DDD to model a complex domain in the simplest possible way. Never use DDD to make your solution more complex.

Your team and management will have to determine if a system you are planning to work on deserves the cost of making a DDD investment.

An **anemic domain model** is a bad thing because you pay most of the high cost of developing a domain model, but you get little or none of the benefit.

### How to DDD

The **ubiquitous language** is a shared team language. It's shared by domain experts and developers alike. In fact, it's shared by everyone on the project team. No matter your role on the team.

The experts and developers work together to craft a model of the domain, they use discussion with both consensus and compromise to achieve the very best language for the project.

The language grows and changes over time as tiny and large breakthroughs are achieved, much like any other living language.

Create a glossary of terms with simple definitions.

The ubiquitous language is a team pattern used to capture the concepts and terms of a specific core business domain in the software model itself. The software model incorporates the nouns, adjectives, verbs, and richer expressions formally formulated and spoken by the close-knit team.

Each of the multiple bounded contexts that integrate has its own ubiquitous language, even though some terms of each may overlap.

### The challenges of applying DDD

If you want to apply DDD completely, with the greatest value to the business, it's going to require more thought and effort, and it's going to take more time.

If you don't get commitment from at least one real expert, you are not going to uncover deep knowledge of the domain.

Most developers have had to change the way they think in order to properly apply DDD.

Honest developers, no matter how experienced, will indicate in a specific case that developing a domain model is, or is not, the best choice.

DDD promotes lightweight development, not ceremonious, heavy, up-front design.

### Fiction, with bucketfuls of reality

*DDD-Lite* is a means of picking and choosing a subset of the DDD tactical patterns, but without giving full attention to discovering, capturing, and enhancing the ubiquitous language. [...] Its focus is much more technical, with a desire to solve technical problems.

## 2. Domains, subdomains, and bounded contexts

![Domains, subdomains, and bounded contexts](https://github.com/backpackerhh/blog-posts/assets/685978/15fb2d4d-3274-4cf2-8435-f8d8615f0d8e)

### Big picture

A domain, in the broad sense, is what an organization does and the world it does it in.

Note that a single bounded context does not necessarily fall within only a single subdomain, but it may.

A **core domain** is a part of the business domain that is of primary importance to the success of the organization. Strategically speaking, the business must excel with its core domain.

Sometimes a bounded context is created or acquired to support the business. If it models some aspect of the business that is essential, yet not core, it is a **supporting subdomain**.

If it captures nothing special to the business, yet is required for the overall business solution, it is a **generic subdomain**.

These kinds of subdomains are important to the success of the business, yet there is no need for the business to excel in these areas.

The way we name a bounded context is in the form *Name-of-Model* Context.

### Real-world domains and subdomains

Your *problem space* is the combination of the core domain and the subdomains it must use.

The *solution space* is one or more bounded contexts, a set of specific software models.

It is a desirable goal to align subdomains one-to-one with bounded contexts. Doing so expressly segregates domain models into well-defined areas of business by objective, melding the problem space with the solution space. In practice this is not always possible, but it can work in a greefield effort.

If we don't understand the vision and goals of the core domain and the areas of the domain that are needed to support it, we won't be able to strategically take advantage of them and avoid associated pitfalls.

### Making sense of bounded contexts

A **bounded context** is an explicit boundary within which a domain model exists. Inside the boundary all terms and phrases of the ubiquitous language have specific meaning, and the model reflects the language with exactness.

A bounded context is principally a linguistic boundary.

The name chosen by each team in each context is always made with the ubiquitous language in mind. You never name a concept arbitrarily, such as to purposely distinguish it from a term in a different context.

When integrations are needed, mapping must be done between bounded contexts.

If you see the exact same objects in multiple contexts, it probably means there is some modeling error, unless the two bounded contexts are using a **shared kernel**.

When there are **user interface** views that render the model and drive execution of its behavior, these are also inside the bounded context. However, this does not mean that we model the domain in the user interface, causing domain model anemia. We want to reject the **Smart UI Anti-Pattern** and any temptation to drag domain concepts that belong in the model into other areas of the system.

Both user interface components and service-oriented endpoints delegate to **application services**. These are different kinds of services, generally providing security and transaction management, and acting as **façade** to the model.

A bounded context should be as big as it needs to be in order to fully express its complete ubiquitous language.

It is best for one well-defined, cohesive team of domain experts and developers to focus on one ubiquitous language modeled in an explicit bounded context. If you assign two or more distinct teams to one bounded context, each team will contribute to a divergent and ill-defined ubiquitous language.

There is the possibility that two teams will cooperate in the design of a shared kernel, which is actually not a typical bounded context. This context mapping pattern forms an intimate relationship between two teams, which requires ongoing consultation when model changes are deemed necessary.

## 3. Context maps

Besides giving you an inventory of systems you must interact with, a **context map** serves as a catalyst for inter-team communication.

A context map captures the existing terrain. [...] If the landscape will change as your current project progresses, you can update the map at that time. First focus on the current situation so you can form an understanding of where you are and determine where to go next.

### Partnership

When teams in two contexts will succeed or fail together, a cooperative relationship needs to emerge. The teams institute a process for coordinated planning of development and joint management of integration. The teams must cooperate on the evolution of their interfaces to accommodate the development needs of both systems. Interdependent features should be scheduled so that they are completed for the same release.

### Shared Kernel

Sharing part of the model and associated code forms a very intimate interdependency, which can leverage design work or undermine it. [...] This explicit shared stuff has special status and shouldn't be changed without consultation with the other team.

### Customer-Supplier Development

When two teams are in an *upstream-downstream* relationship, where the upstream team may succeed interdependently of the fate of the downstream team, the needs of the downstream team come to be addressed in a variety of ways with a wide range of consequences. Downstream priorities factor into upstream planning.

### Conformist

When two development teams have an *upstream/downstream* relationship in which the upstream team has no motivation to provide for the downstream team's needs, the downstream team is helpless. Altruism may motivate upstream developers to make promises, but they are unlikely to be fulfilled. The downstream team eliminates the complexity of translation between bounded contexts by slavishly adhering to the model of the upstream team.

### Anticorruption Layer

Translation layers can be simple, even elegant, when bridging well-designed bounded contexts with cooperative teams. [...] As a downstream client, create an isolating layer to provide your system with functionality of the upstream system in terms of your own domain model. This layer talks to the other system through its existing interface, requiring little or no modification to the other system.

A domain service can be defined in the downstream context for each type of anticorruption layer.

You may also put an anticorruption layer behind a repository interface. If using REST, a client domain service implementation accesses a remote open host service. Server responses produce representations as a published language. The downstream anticorruption layer translates representations into domain objects of its local context.

When the remote model's representation is received, the boundary objects grab the content of interest out of the representation and translate it, creating the appropriate value object instance.

### Open Host Service

Define a protocol that gives access to your subsystem as a set of services. Open the protocol so that all who need to integrate with you can use it.

Can be implemented as REST-based resources that client bounded contexts interact with. We generally think of this service as a remote procedure call (RPC) API, but it can be implemented using message exchange.

### Published Language

The translation between the models of two bounded contexts requires a common language. Use a well-documented shared language that can express the necessary domain information as a common medium of communication, translating as necessary into and out of that language.

Hypermedia makes a published language very dynamic and interactive, enabling clients to navigate to sets of linked resources.

It's also used in an *Event-Driven Architecture*, where domain events are delivered as messages to subscribing interested parties.

### Separate Ways

Integration is always expensive, and sometimes the benefit is small. Declare a bounded context to have no connection to the others at all, enabling developers to find simple, specialized solutions within this small scope.

### Big Ball of Mud

There are parts of systems, often large ones, where models are mixed and boundaries are inconsistent. Draw a boundary around the entire mess and designate it a **Big Ball of Mud**. Do not try to apply sophisticated modeling within this context.

By cleanly separating bounded contexts, we are able to keep each context pure, while applying data from other contexts to express concepts in our own.

Keep discussions about the project flowing back to your map to stimulate useful refinements.

## 4. Architecture

One of the big advantages of DDD is that it doesn't require the use of any specific architecture.

The goal is to use just the right choices and combinations of architecture and architecture patterns.

Avoiding architectural style and pattern overuse is just as important as using the right ones.

We use architecture only to mitigate the risk of failure, not to increase our risk of failure by using an architectural style or pattern that cannot be justified.

You cannot determine the necessary software qualities without functional requirements.

### Layers

A **Strict Layers Architecture** allows coupling only to the layer directly below.

A **Relaxed Layers Architecture** allows any higher-level layer to couple to any layer below it.

#### Dependency Inversion Principle

A component that provides low-level services (Infrastructure) should depend on interfaces defined by high-level components (User Interface, Application, and Domain).

![Dependency Inversion Principle](https://github.com/backpackerhh/blog-posts/assets/685978/c3af2627-fbf4-43c4-b89d-0ec8ef84fda8)

Focusing on the *Domain Layer*, using DIP enables both the domain and infrastructure to depend on abstractions (interfaces) defined by the domain model. Since the *Application Layer* is the direct client of the domain, it depends on domain interfaces and indirectly accesses repository and any technical domain service implementation classes provided by infrastructure. It may use any one of a few ways to acquire the implementations, such as **dependency injection**.

### Hexagonal or Ports and Adapters

An adapter is created to transform application results into a form accepted by a specific output mechanism.

It's not that dependency injection is automatically **Hexagonal** but it encourages a way of producing an architecture that leans naturally toward the development of a **Ports and Adapters** style.

The outside enables disparate clients to submit input and also provides mechanisms to retrieve persisted data, store the application's output (e.g., a database), or send it elsewhere along its way (e.g., messaging).

![Hexagonal Architecture a.k.a. Ports and Adapters](https://github.com/backpackerhh/blog-posts/assets/685978/d3dfd641-361e-406b-9a21-bff4721a3717)

Each of the hexagon's sides represents a different kind of port, for either input or output.

In whatever way ports are partitioned, client requests arrive and the respective adapter transforms their input. It then invokes an operation on the application or sends the application an event. Control is thus transferred to the inside.

When using Hexagonal, we design the application with our use cases in mind, not the number of supported clients.

The application's API is published as a set of application services.

The entire application and domain model can be designed and tested before clients and storage mechanisms exist.

Long before a persistence mechanism is selected for the project, in-memory repositories can be employed to mimic persistence for the sake of testing.

Because the Hexagonal Architecture is versatile, it could well be the foundation that supports other architectures required by the system, such as Service-Oriented, REST, or an Event-Driven Architecture.

### Representational State Transfer (REST)

Alternative approaches for combining DDD and RESTful HTTP:

* The first approach is to create a separate bounded context for the system's interface layer and use appropriate strategies to access the actual core domain from the system's interface model.

* Another approach [...] if specific media types are developed to support not only a single system interface but a category of similar client-server interactions, a domain model can be created to represent each standard media type.

The more specialized the solution, the more useful the first approach turns out to be. The more generally useful the solution is, with the extreme end of the spectrum being standardization by an official standards body, the more sense it makes to go with the second, media-type-centric approach.

### Command-Query Responsibility Segregation (CQRS)

If a method modifies the state of the object, it is a **command**, and its method must not return a value.

If a method returns some value, it is a **query**, and it must not directly or indirectly cause the modification of the state of the object.

Aggregates would have no query methods (getters), only command methods. Repositories would be stripped down to an `add()` or `save()` method (supporting both creation and updating saves) and only a single query method, such as `fromId()`. The single query method takes the unique identity of an aggregate and returns it. A repository could not be used to find an aggregate by any other means, such as by filtering on some additional properties. With all of that removed from the traditional model, we designate it a **command model** or **write model**.

We still need a way to display data to the user. For that we create a second model, one that is tuned for optimized queries. That's our **query model** or **read model**.

The command model is persisted in one store and the query model in another.

![Command-Query Responsibility Segregation (CQRS)](https://github.com/backpackerhh/blog-posts/assets/685978/3a87b465-c509-4fc5-b60f-5447223f266e)

The query model is a denormalized data model. It is not meant to deliver domain behavior, only data for display (and possibly reporting). If this data model is a SQL database, each table would hold the data for a single kind of client view (display).

It's worth noting that CQRS-based views can be both cheap and disposable (for development and in maintenance). This is especially so if you use a simple form of *Event Sourcing* and save all events into a persistent store, which can be republished at any time to create new persistent view data.

Any single view could be rewritten from scratch in isolation or the entire query model be switched to completely different persistence technology. This makes it easy to create and maintain views that continuously address ongoing UI needs.

The data model design should follow, as much as possible, the pattern of one table per user interface view type, with as many table views as necessary to reflect application security roles.

A basic database table view has no overhead when performing updates on the backing table. [...] Only *materialized views* incur update overhead since the view's data must be copied into one place so it is ready for selects.

The user interface must collect the data necessary to correctly parameterize the command. This implies that much thought must be given to user experience design.

We can use a *categorized style* with several command handlers in one application service.

We can create a *dedicated style* handler. Each one would be a single class with one method.

The *messaging style* of command handler where each command is sent as an asynchronous message and delivered to a handler designed with the dedicated style. [...] Switch to asynchronous only if scalability demands require it.

Do not allow any one handler to depend on (make use of) any others.

When the command handler completes, a single aggregate instance has been updated and a domain event has been published by the command model.

The published event may also be used to cause the synchronization of other aggregate instances effected by this one command, but the modification of the additional aggregate instances would be eventually consistent with the one committed by this transaction.

If a command was delivered by "at-least-once" messaging and the application ensures idempotent operations, the redelivered message is silently dropped.

A special subscriber registers to receive all domain events published by the command model. The subscriber uses each domain event to update the query model to reflect the most recent changes to the command model. This implies that each event must be rich enough to supply all the data necessary to produce the correct state in the query model.

To update synchronously, the query model and command model would normally share the same database (or schema), and we would update the two models in the same transaction. That keeps both models completely consistent.

If the system is normally under heavy load and the query model update process is lengthy, use asynchronous updates instead.

Design the user interface to temporarily display the data that was successfully submitted as parameters of the command just executed. This is a bit of a trick, but it enables the user to immediately see what will eventually be reflected in the query model. It may be the only way to ensure that the user interface does not display completely stale data just after a command is successfully executed.

Always explicitly displays on the user interface the date and time of the data from the query model that a user is currently viewing. To do so, each record in the query model needs to maintain the date and time of the latest update. This is a trivial step, generally supported by a database trigger. With the date and time of the latest update, the user interface can now inform the user how old the data is. If the user determines that the data is too stale to use, they can at that time request fresher data.

Addressing delays may even be as easy as informing users that their request has been accepted and a result will require some processing time.

### Event-Driven Architecture

Event-driven architecture (EDA) is a software architecture promoting the production, detection, consumption of, and reaction to events.

The various domain events received have a specific meaning in each receiving bounded context, or possibly no meaning at all. If the event type is of interest in a specific context, its properties are adapted to the application's API and used to execute an operation there. The command operation executed on the application's API is then reflected into the domain model according to its protocol.

#### Long-Running Processes (a.k.a. Sagas)

Approaches to designing a **long-running process**:

* Design the process as a composite task, which is tracked by an executive component that records the steps and completeness of the task using a persistent object.

* Design the process as a set of partner aggregates that collaborate in a set of activities. One or more aggregate instances act as the executive and maintain the overall state of the process.

* Design a stateless process in that each message handler component that receives an event-carrying message must enrich the received event with more task progress information as it sends the next message. The state of the overall process is maintained only in the body of each message sent from collaborator to collaborator.

Assign a unique process identity that is carried by each of the associated domain events. This could be the same identity assigned to the originating domain event that causes the long-running process to begin.

Use a universally unique identifier (UUID) allocated specifically to the process.

#### Event Sourcing

![Event Sourcing](https://github.com/backpackerhh/blog-posts/assets/685978/c166bc21-3008-4325-8746-7ebfe69ad817)

Each of the events is saved to an **event store** in the order in which it occurred. When each aggregate is retrieved from its repository, the instance is reconstituted by playing back the events in the order in which they previously occurred.

A process is developed to produce, in the background, a snapshot of the aggregate's in-memory state at a specific point in event store history.

The aggregate is loaded into memory using all previous events to the current point in time. The aggregate state is then serialized, and the serialized snapshot image is then saved to the event store. From that point forward the aggregate is first instantiated using the most recent snapshot, and then all events newer than that snapshot are played back on the aggregate as described previously.

Snapshots are not created randomly. Rather, they can be created at points where a predefined number of newer events have occurred.

Because events are often persisted in an event store as binary representations, they cannot (optimally) be used for queries.

## 5. Entities

We design a domain concept as an **entity** when we care about its individuality, when distinguishing it from all other objects in a system is a mandatory constraint.

When an object is distinguished by its identity, rather than its attributes, make this primary to its definition in the model. Keep the class definition simple and focused on life cycle continuity and identity.

### Unique identity

An entity's unique identity may or may not also be practical for finding or matching.

UUID is a relatively fast identity to generate, requiring no interaction with the outside, such as a persistence mechanism. Even if a specific kind of entity is created many times per second, the UUID generator can keep up the pace.

Entities held inside an **aggregate** need only have uniqueness among other entities held inside the same aggregate. On the other hand, the entity serving as an **aggregate root** requires global unique identity.

There is no need for the domain identity to play the role of database primary key.

Surrogate database primary keys can be used throughout the data model as foreign keys in other tables, providing referential integrity.

In most cases unique identity must be protected from modification, remaining stable throughout the lifetime of the entity to which it is assigned.

### Discovering entities and their intrinsic characteristics

After an entity is discovered and named, uncover the attributes/properties that uniquely identify it and enable it to be found.

We can leverage interfaces to hide implementation details that we don't want leaking out of the model to clients. Design an interface to expose exactly what we want to allow clients to use, and nothing more.

Sometimes an entity maintains one or more **invariants**. An invariant is a state that must stay transactionally consistent throughout the entity life cycle.

Use a **factory** for complex entity instantiations.

Just because all of the attributes/properties of a domain object are individually valid, that does not mean that the object as a whole is valid. Maybe the combination of two correct attributes invalidates the whole object.

Design by contract enables us to specify the preconditions, postconditions, and invariants of the components we design.

To validate a whole entity, we need to have access to the state of the entire object - all of its attributes/properties.

Embedding validation inside an entity gives it too many responsibilities.

If the validation class is not placed in the same module as the entity, all attribute/property accessors must be public, which is undesirable in many cases.

The validation class can implement the *Specification pattern* or the *Strategy pattern*.

It is important for the validation process to collect a full set of results rather than throw an exception at the first sign of trouble.

The entity needs to know nothing about how it is validated, only that it can be validated.

A domain service can use repositories to read the aggregate instances it needs to validate. It can then run each instance through its paces, separately or in combination with others.

On occasion an aggregate or a set of aggregates is in a temporary, intermediate state. Perhaps we could model a status on an aggregate to indicate this, preventing validation at inappropriate times. When the conditions are ripe for validation, the model could inform clients by publishing a domain event.

## 6. Value objects

When you care only about the attributes of an element of the model, classify it as a **value object**.

We should strive to model using value objects instead of entities wherever possible.

Treat the value object as immutable. Don't give it any identity and avoid the design complexities necessary to maintain entities.

It is actually a concept that measures, quantifies, or otherwise describes a thing in the domain.

You must not allow the attributes of a value instance to be populated after construction, as if to build up the *whole value* piece by piece. Instead, the final state must be guaranteed to initialize all at once, atomically.

In your model an immutable value should be held as a reference by an entity as long as its constant state describes the currently correct *whole value*. If that is no longer true, the entire value is completely replaced with a new value that does represent the currently correct whole.

Where possible use value objects to model concepts in the downstream context when objects from the upstream context flow in.

Measures and descriptions are best modeled as values.

If there is no need to maintain a continuity of change over the life cycle of descriptive types in your bounded context, model them as values.

## 7. Services

A service in the domain is a stateless operation that fulfills a domain-specific task. Often the best indication that you should create a service in the domain model is when the operation you need to perform feels out of place as a method on an aggregate or a value object.

As a rule of thumb, we should try to avoid the use of repositories from inside aggregates, if at all possible.

Since it may be dealing with multiple domain objects in a single, atomic operation, it would have the latitude to scale up a bit in complexity.

Make sure the service has an interface that clearly expresses the ubiquitous language in its bounded context.

Using services overzealously will usually result in the negative consequences of creating an **anemic domain model**, where all the domain logic resides in services rather than mostly spread across entities and value objects.

Knowledge that is purely domain specific should never be leaked out into clients, even if the client is an application service. [...] The only business responsibility that the client should have is to coordinate the use of a single domain-specific operation that handles all other details of the business problem.

### Modeling a service in the domain

If your implementation class has specific decoupling goals because you are providing multiple specific implementations, name the class according to its specialty. The need to name each specialized implementation carefully is proof that specialties exist in your domain.

A inversion-of-control container injects the service instance. Since the client never instantiates the service, it isn't aware that the interface and implementation are either combined or separated.

A service in the domain is welcome to use repositories as needed, but accessing repositories from an aggregate instance is not a recommended practice.

Address transactions and security as application concerns in application services, not in domain services.

### Testing services

The repository used could be the full implementation, an in-memory variety, or mocked. It works fine to test with the full implementation if it is fast enough, as long as the test ends with a rollback of the transaction, preventing the buildup of extraneous instances across tests.

## 8. Domain events

A **domain event** is a full-fledged part of the domain model, a representation of something that happened in the domain.

There will probably be times when the spoken language of the experts doesn't lead to a clear reason to model an event, yet the business situation may still call for it. [...] As such events are handled by subscribers, they may have far-reaching impact on local and remote bounded contexts.

Although domain experts may not initially be aware of the need for every kind of event, they should understand the reasons for them as they are included in discussions about specific events. Once there is clear consensus, new events become a formal part of the ubiquitous language.

One rule of aggregates states that only a single instance should be modified in a single transaction, and all other dependent changes must occur in separate transactions. So other aggregate instances in the local bounded context may be synchronized using this approach.

It allows us to achieve loose coupling between systems.

![Domain Events](https://github.com/backpackerhh/blog-posts/assets/685978/78826999-4b4e-49a2-9ff0-3b4a21841540)

### Modeling events

When modeling events, name them and their properties according to the ubiquitous language in the bounded context where they originate. If an event is the result of executing a command operation on an aggregate, the name is usually derived from the command that was executed.

The event name states what occurred (past tense) in the aggregate after the requested operation succeeded.

An event is usually designed as immutable.

Subscribers avoid querying back on the aggregate from which the event was published, which could be needlessly difficult or expensive.

Sometimes events are designed to be created by direct request from clients. This is done in response to some occurrence that is not the direct result of executing behavior on an instance of an aggregate in the model. Possibly a user of the system initiates some action that is considered an event in its own right. When that happens, the event can be modeled as an aggregate and retained in its own repository. Since it represents some past occurrence, its repository would not permit its removal.

Unique identity may be necessary when events are published outside the local bounded context where they occur, when messaging infrastructure forwards them along. In some situations individual messages can be delivered more than once. This would happen if the message sender crashes before the messaging infrastructure confirms that the message was sent.

Whatever may cause a message's redelivery, the solution is to get the remote subscribers to detect duplicate message delivery and ignore messages already received.

Remote subscribers can use the unique identity to manage de-duplication when messages are delivered more than once.

### Publishing events from the domain

![Publishing events from the domain](https://github.com/backpackerhh/blog-posts/assets/685978/083a7d22-4ee6-495e-8ed4-8e5a95c0801c)

Since application services are the direct client of the domain model when using Hexagonal Architecture, they are in an ideal position to register a subscriber with the publisher before they execute event-generating behavior on aggregates.

Don't use the event notification to modify a second aggregate instance.

Events are a domain-wide concept, not just a concept in a single bounded context.

### Spreading the news to remote bounded contexts

The use of any messaging mechanism between bounded contexts requires that we adopt a commitment to **eventual consistency**. [...] The changes in one model that influence changes in one or more other models will not be fully consistent for some elapsed period of time.

At least two mechanisms in a messaging solution must always be consistent with each other: the persistence store used by the domain model, and the persistence store backing the messaging infrastructure used to forward the events published by the model. [...] Ensure that when the model's changes are persisted, event delivery is also guaranteed, and that if an event is delivered through messaging, it indicates a true situation reflected by the model that published it.

Using domain events allows any number of your enterprise systems to be designed as autonomous services and systems.

The event will hold some limited amount of command parameters and/or aggregate state that will convey enough meaning to allow subscribing bounded contexts to react correctly.

If you must nearly replicate the concepts, objects, and their associations from the foreign model in your own model, you may need to consider sticking with RPC.

We must ask how long between consistent states is acceptable, and how much delay is too great. Domain experts will likely be very much in tune with what constitutes acceptable and unacceptable delays.

### Event store

Use the **event store** as a queue for publishing all domain events through a messaging infrastructure.

An event store is not just an audit log.

Unless an event store is maintained from the start, the historical data will be unavailable as needs arise.

Use the events to reconstitute each aggregate instance when it is retrieved from its repository.

### Implementation

A notification is an application concern, not a domain concern, even though the events being published as notifications originated in the model.

The event identity along with the type attribute allows us to publish the same notifications at different times to any number of topics/channels.

De-duplication is a necessity in environments where a single message published through a messaging system could possibly be delivered to subscribers more than once.

The receiver can be designed to refuse to execute an operation in response to a duplicate message.

One way to accomplish that is to allocate an area in the subscriber's persistence mechanism to save the name of the topic/exchange along with the unique message ID of all handled messages.

Then you can query for duplicates before handling each message. If the query finds that a message was already handled, the subscriber simply ignores it.

The handled message tracking is not part of the domain model.

A de-duplication query that checks for message IDs less than the most recent one would cause you to ignore some messages that were received out of order.

## 9. Modules

Modules in your model serve as named containers for domain object classes that are highly cohesive with one another. The goal should be low coupling between the classes that are in different modules.

Give modules names that become part of the ubiquitous language.

Rename existing modules with the same boldness as when creating new ones. Always assertively place fresh and freshened domain concepts into the modules that contemporary insight calls for.

In cases where terminology is fuzzy and it is not clear if contextual boundaries should be created, first consider the possibility of keeping them together.

Use modules to modularize cohesive domain objects, and to separate those that are not cohesive or less cohesive.

Boundaries between models are clearly justified, as the linguistics demand.

## 10. Aggregates

### Rule: Model true invariants in consistency boundaries

When trying to discover the aggregates in a bounded context, we must understand the model's true invariants. Only with that knowledge can we determine which objects should be clustered into a given aggregate.

An invariant is a business rule that must always be consistent.

There are different kinds of consistency:

* **Transactional consistency**, which is considered immediate and atomic.
* **Eventual consistency**.

When discussing invariants, we are referring to transactional consistency.

When employing a typical persistence mechanism, we use a single transaction to manage consistency. When the transaction commits, everything inside one boundary must be consistent.

A properly designed aggregate is one that can be modified in any way required by the business with its invariants completely consistent within a single transaction.

A properly designed bounded context modifies only one aggregate instance per transaction in all cases.

The user interface should concentrate each request to execute a single command on just one aggregate instance.

### Rule: Design small aggregates

Limit the aggregate to just the root entity and a minimal number of attributes and/or value-typed properties. The correct minimum is however many are necessary, and no more. Those that must be consistent with others, even if domain experts don't specify them as rules.

Depending on your persistence mechanism, values can be serialized with the root entity, whereas entities can require separately tracked storage.

Smaller aggregates not only perform and scale better, they are also biased toward transactional success, meaning that conflicts preventing a commit are rare. This makes a system more usable.

Trying to keep multiple aggregate instances consistent may be telling you that your team has missed an invariant. You may end up folding the multiple aggregates into one new concept with a new name in order to address the newly recognized business rule.

Just because you are given a use case that calls for maintaining consistency in a single transaction doesn't mean you should do that. Often, in such cases, the business goal can be achieved with eventual consistency between aggregates.

### Rule: Reference other aggregates by identity

One aggregate may hold references to the root of other aggregates. However, we must keep in mind that this does not place the referenced aggregate inside the consistency boundary of the one referencing it. The reference does not cause the formation of just one whole aggregate.

Prefer references to external aggregates only by their globally unique identity, not by holding a direct object reference.

If you don't hold any reference, you can't modify another aggregate.

Aggregates with inferred object references are thus automatically smaller because references are never eagerly loaded. The model can perform better because instances require less time to load and take less memory.

Use a repository or domain service to look up dependent objects ahead of invoking the aggregate behavior.

Having an application service resolve dependencies frees the aggregate from relying on either a repository or a domain service. However, for very complex and domain-specific dependency resolutions, passing a domain service into an aggregate command method can be the best way to go. The aggregate can then double-dispatch to the domain service to resolve references. Again, in whatever way one aggregate gains access to others, referencing multiple aggregates in one request does not give license to cause modification on two or more of them.

Limiting a model to using only reference by identity could make it more difficult to serve clients that assemble and render user interface views. You may have to use multiple repositories in a single use case to populate views. If query overhead causes performance issues, it may be worth considering the use of theta joins or CQRS.

Message subscribers in foreign bounded contexts use the identities to carry out operations in their own domain models. Reference by identity forms remote associations or partners.

Transactions across distributed systems are not atomic. The various systems bring multiple aggregates into a consistent state eventually.

### Rule: Use eventual consistency outside the boundary

Any rule that spans aggregates will not be expected to be up-to-date at all times. Through event processing, batch processing, or other update mechanisms, other dependencies can be resolved within some specific time.

If executing a command on one aggregate instance requires that additional business rules execute on one or more other aggregates, use eventual consistency. Accepting that all aggregate instances in a large-scale, high-traffic enterprise are never completely consistent helps us accept that eventual consistency also makes sense in the smaller scale where just a few instances are involved.

Domain experts are often willing to allow for reasonable delays, a generous number of seconds, minutes, hours, or even days, before consistency occurs.

Each of the subscribers executes in a separate transaction, obeying the rule of aggregates to modify just one instance per transaction.

The modification can be retried if the subscriber does not acknowledge success to the messaging mechanism. The message will be redelivered, a new transaction started, a new attempt made to execute the necessary command, and a corresponding commit made. This retry process can continue until consistency is achieved, or until a retry limit is reached. If complete failure occurs, it may be necessary to compensate, or at a minimum to report the failure for pending intervention.

When examining the use case (or story), ask whether it's the job of the user executing the use case to make the data consistent. If it is, try to make it transactionally consistent, but only by adhering to the other rules of aggregates. If it is another user's job, or the job of the system, allow it to be eventually consistent.

### Reasons to break the rules

You may experience user interface design decisions, technical limitations, stiff policies, or other factors in your enterprise environment that require you to make some compromises.

#### Reason 1: User interface convenience

Sometimes user interfaces, as a convenience, allow users to define the common characteristics of many things at once in order to create batches of them. [...] If creating a batch of aggregate instances all at once is semantically no different from creating one at a time repeatedly, it represents one reason to break the rule of thumb with impunity.

#### Reason 2: Lack of technical mechanisms

With no messaging mechanism, no background timers, and no other home-grown threading capabilities, what could be done?

Ensuring user-aggregate affinity makes the decision to alter multiple aggregate instances in a single transaction more sound since it tends to prevent the violation of invariants and transactional collisions. Even with user-aggregate affinity, in rare situations users may face concurrency conflicts. Yet each aggregate would still be protected from that by using optimistic concurrency.

When our design is forced to, sometimes it works out well to modify multiple aggregate instances in one transaction.

#### Reason 3: Global transactions

Even if you must use a global transaction, you don't necessarily have to modify multiple aggregate instances at once in your local bounded context.

The downside to global transactions is that your system will probably never scale as it could if you were able to avoid two-phase commits and the immediate consistency that goes along with them.

#### Reason 4: Query performance

There may be times when it's best to hold direct object references to other aggregates. This could be used to ease repository query performance issues.

### Implementation

#### Law of Demeter

This guideline emphasizes the principle of least knowledge.

Any given method on any object may invoke methods only on the following:

1. itself.
2. any parameters passed to it.
3. any object it instantiates.
4. self-contained part objects that it can directly access.

The **Law of Demeter** disallows all navigation into aggregate parts beyond the root.

#### Tell, Don't Ask

This guideline simply asserts that objects should be told what to do.

The use of **Tell, Don't Ask** allows for navigation beyond the root but does stipulate that modification of the aggregate state belongs to the aggregate, not the client.

#### Optimistic concurrency

Versioning all entity parts doesn't work in every case. Sometimes the only way to protect an invariant is to modify the root version. This can be accomplished more easily if we can modify a legitimate property on the root. In this case, the root's property would always be modified in response to a deeper part modification, which in turn causes the ORM to increment the root's version.

We don't want infrastructural concerns to drive modeling decisions.

#### Avoid dependency injection

Dependency injection of a repository or domain service into an aggregate should generally be viewed as harmful.

Preferably dependent objects are looked up before an aggregate command method is invoked, and passed in to it.

## 11. Factories

A factory may or may not have additional responsibilities in the domain model other than object creation.

An aggregate root that provides a factory method for producing instances of another aggregate type (or inner parts) will have the primary responsibility of providing its main aggregate behavior, the factory method being just one of those.

Placing a carefully designed factory method on specific aggregate roots can ensure that tenancy and other association identities are created correctly.

One case where you would find an **abstract factory** of great benefit is when creating objects of different types in a class hierarchy.

If you decide to use class hierarchies in your design, be prepared for the potential for pain that could result.

It is unnecessary to guard the factory method itself since the constructors of each of the value parameters and the aggregate constructor, as well as the setter methods that the constructor self-delegates to, provide all the needed guards.

If our design were to support only a public constructor on the aggregate, it would reduce the expressiveness of the model and we would not be able to explicitly model that part of the language of the domain. Using this design requires the full aggregate constructor to be hidden from clients.

Factory methods on aggregates can be used to express the language in context, reduce the burden on clients when creating new aggregate instances, and ensure instantiations with correct state.

## 12. Repositories

Placing an aggregate instance in its corresponding repository, and later using that repository to retrieve the same instance, yields the expected whole object. If you alter a preexisting aggregate instance that you retrieve from the repository, its changes will be persisted. If you remove the instance from the repository, you will be unable to retrieve it from that point forward.

Every persistent aggregate type will have a repository. Generally speaking, there is a one-to-one relationship between an aggregate type and a repository. However, sometimes when two or more aggregate types share an object hierarchy, the types may share a single repository.

Only aggregates have repositories.

If you are retrieving and using entities directly in an ad hoc fashion rather than crafting aggregate transactional boundaries, you may prefer to avoid repositories.

### Collection-oriented repositories

Very closely mimic a collection, simulating at least some of its standard interface. Here you design a repository interface that does not hint in any way that there is an underlying persistence mechanism, avoiding any notion of saving or persisting data to a store.

It is the aggregate root's unique identity that allows the Set-like repository to prevent adding the same aggregate instances more than once.

You'd just retrieve from the collection the reference to the object you desire to modify, and then ask the object to execute some state-transitioning behavior by invoking a command method.

You need to define a public interface and at least one implementation. [...] Place the interface definition in the same module as the aggregate type that it stores.

Use the **Dependency Inversion Principle** (DIP) for layering infrastructure concerns. The *Infrastructure Layer* is logically above all others, making references unidirectional and downward to the *Domain Layer*.

Since we are going to the trouble of abstracting away the implementation details of the underlying persistence framework, we want to insulate clients from all such details, including exceptions.

There may be cases where adding and/or removing multiple aggregate instances in one transaction isn't appropriate. When that is true of a given case in your domain, don't include methods `addAll()` and `removeAll()`. [...] A client can always use a loop to invoke `add()` or `remove()` multiple times when iterating over a collection on its own.

From a business perspective it may be unwise, ill advised, or even illegal to remove some objects. In those cases you may decide to simply mark the aggregate instance disabled, unusable, or, in some other domain-specific way, logically removed. If so, you may determine not to include any removal methods on the repository public interface, or you may decide to implement the removal methods to set the unusable state of the aggregate instance.

### Persistence-oriented repositories

You will need to employ a persistence-oriented repository when your persistence mechanism doesn't implicitly or explicitly detect and track object changes. This happens to be the case when using an in-memory *data fabric*, or by any other name a NoSQL key-value data store. Every time you create a new aggregate instance or change a preexisting one, you will have to put it into the data store by using `save()` or a save-like repository method.

In cases where it is a very realistic possibility that your persistence mechanism will shift in the future, it might be best to design with the more flexible interface in mind. The downside is that your current object-relational mapper may cause you to leave out necessary uses of `save()` that you may catch only later when there is no longer a backing *unit of work*. The upside is that the repository pattern will allow you to completely replace your persistence mechanism with potentially little impact on your application.

When using a collection-oriented style, aggregate instances are added only when they are created. When using a persistence-oriented style, aggregate instances must be saved both when they are created and when they are modified.

### Additional behavior

A *use case optimal query* can be used to specify a complex query against the persistence mechanism, dynamically placing the results into a value object specifically designed to address the needs of the use case.

A repository that provides a `size()` method answers a very simple value in the form of an integer count of the total aggregate instances it holds. A use case optimal query is just extending this notion a bit to provide a somewhat more complex value, one that addresses more complex client demands.

If you find that you must create many finder methods supporting use case optimal queries on multiple repositories, it's probably a code smell. [...] The code smell here might be called *repository masks aggregate mis-design*.

### Managing transactions

The domain model and its encompassing *Domain Layer* is never the correct place to manage transactions. The operations associated with a model are usually too fine grained to themselves manage transactions and shouldn't be aware that transactions play a part in their life cycle.

When a façade's business method is invoked by the *User Interface Layer* the business method begins a transaction and then acts as a client to the domain model. After all necessary interaction with the domain model is successfully completed, the façade's business method commits the transaction it started. If an error/exception occurs that prevents completion of the use case task, the transaction is rolled back by the same managing business method.

To enlist changes to the domain model in a transaction, ensure that repository implementations have access to the same *session* or *unit of work* for the transaction that the *Application Layer* started. That way the modifications made in the *Domain Layer* will be properly committed to the underlying database or rolled back.

If you aren't careful, what works well in development and test can fail severely in production because of concurrency issues.

### Repository vs Data Access Object

DAO and related patterns tend to serve as wrappers around database tables, providing CRUD interfaces to them. On the other hand, repository and *data mapper*, having object affinity, are typically the patterns that would be used with a domain model.

As much as possible you should try to design your repositories with a collection orientation rather than a data access orientation. That will help keep you focused on the domain as a model rather than on data and any CRUD operations that may be used behind the scenes to manage its persistence.

### Testing repositories

You have to test the repositories themselves in order to prove that they work correctly. You also must test code that uses repositories to store the aggregates that they create and to find preexisting ones.

If it is very difficult to set up the full persistent implementation of a repository for test, or too slow to use it, you can leverage another approach. You may also face undesirable conditions early on during domain modeling, perhaps when your persistence mechanisms, including the database schema, are not yet available. When you face any of these situations, it works best to implement an in-memory edition of repositories.

## 13. Integrating bounded contexts

A straightforward approach is for a bounded context to expose an application programming interface (API), and another bounded context to use that API via remote procedure calls (RPCs).

A second approach is through the use of a messaging mechanism. Each of the systems that need to interact do so through the use of a message queue or a *Publish-Subscribe* mechanism.

A third approach is by using RESTful HTTP for exchanging and modifying resources that are uniquely identified using a distinct URI. Various operations can be performed on each resource.

We must create some form of contract between the data-exchanging systems, and possibly even the mechanisms to parse or interpret those structures, so they can be consumed. [...] Some options are to use XML, JSON, or a specialized format such as Protocol Buffers.

We can define a reliable contract using a standards-based approach, which actually forms a **published language**. One such standard approach is to define a custom media type. [...] The specification defines the binding contract between producers and consumers and offers a foolproof means to exchange such media without sharing the interface and class binaries.

The consuming bounded context should be interested only in the data properties and should never be tempted to use functionality that is part of a different model. The consumer's port adapters should shield its domain model from any such dependencies and must instead pass needed event data as appropriate parameters with types as defined only in its own bounded context. Any necessary calculations or processing should be performed by the producing bounded context and provided as enriching event data attributes.

You really have to think in terms of the version compatibility and plan for smart modifications to new versions so you don't break most consumers.

Depending on the number of teams, bounded contexts, change ratio, and other factors, it might work out to share classes and interfaces when your project is starting, but it could be better to use a more decoupled, custom media type contract in the production stage. [...] Sometimes what a team starts out with ends up being what they live with ongoing, and they never take the time to make a 180-degree change.

### Integration using RESTful resources

When a bounded context provides a rich set of RESTful resources through URIs, it is a kind of **open host service**.

HTTP and REST certainly form an open protocol allowing all who need to integrate with the subsystem to do so.

If the REST-based bounded context becomes unavailable for some reason, dependent client bounded contexts will be unable to carry out necessary integration operations during any downtime.

Even when RESTful (or RPC for that matter) is your only means to integrate, you can create the illusion of temporal decoupling by using timers or messaging in your own system. That way your system will reach out to any remote systems only when a timer elapses or when a message is received. If the remote system is unavailable, the timer threshold can be backed off, or if using messaging the message can be negatively acknowledged to the broker and redelivered.

Publishing a **shared kernel** or accepting a **conformist** relationship puts consumers into a tightly coupled integration with the consumed domain model. Those kinds of relationships should be avoided if at all possible.

You provide only what integrators need at present, and you understand those needs only by considering a range of use case scenarios.

Shielding the integrators from the details of understanding the domain model would ultimately increase their productivity and make their dependent bounded contexts more maintainable.

The integrating consumer of this RESTful resource can translate it into the specific kind of domain object needed by its bounded context.

An interface completely abstracts away the complexity of the remote system access and subsequent translations from the published language to objects that adhere to the local ubiquitous language.

Create the separated interface in the inner hexagon as part of the domain model. Yet, the implementation is technical and is housed at the outside of the Hexagonal Architecture, where the Ports and Adapters reside.

Generally an **anticorruption layer** will have a specialized adapter and a translator.

If our goal is to produce an aggregate from an anticorruption layer, a repository may be a more natural source.

### Integration using messaging

A message-based approach to integration can allow any one system to achieve a higher degree of autonomy from systems it depends on. As long as the messaging infrastructure remains operational, messages can be sent and delivered even when any one system is unavailable.

There will tend to be several or even many such events that occur in each system, and you will create a unique kind of event as a means to record each.

Note that publishers and subscribers should consider the use of fully qualified class names, which includes the module name and the class name. This removes all possible collision or ambiguity that could exist with same or similarly named events from different bounded contexts.

Having the goal to reduce the amount of foreign information we take responsibility for will make our jobs much easier. It's integrating with a minimalistic mindset.

Identity is safe to share because it is immutable. We can even use aggregate disabling and soft deletions to ensure that referenced objects never disappear.

Events must provide enough information to inform consumers of the kinds of steps that they must take in response to past facts. Still, it is possible for event data to be used to perform calculations and derive state in consuming foreign bounded contexts while not actually holding on to and assuming the responsibility for keeping it synchronized with its official state located in the system of record.

It is the responsibility of listeners to determine whether or not to actually do something in response to an event.

If there is a problem with the message send, care must be taken to ensure that a resend is attempted until it succeeds.

When a messaging mechanism is offline for some time, notification publishers will be unable to send messages through it. Since this situation may be detected by the publishing client, it would likely work best to back off attempts to send notifications until the messaging system is available once again.

It could make sense to back off as much as 30 seconds or a minute between retries. Remember, if your system has an event store, your events will continue to be queued in your live system and can be sent as soon as messaging is available again.

If automatic recovery of consumers is not supported, you will need to be certain that your consumers are reregistered. Otherwise, you will eventually make the unwanted discovery that your bounded context isn't receiving the notifications that are necessary to keep it interacting with the bounded contexts it depends on. That's one kind of eventual consistency that you want to avoid.

## 14. Application

A domain model often lives at the heart of an application. The application may have a user interface that presents concepts of the domain model and allows the user to perform various actions on the model. The user interface will make use of application-level services that coordinate use case tasks, manage transactions, and assert necessary security authorizations. Further, the user interface, application services, and domain model will rely on enterprise platform-specific infrastructural support. The infrastructure implementation details will generally include the facilities of a component container, application management, messaging, and database.

Generic and supporting subdomains will sometimes lack all the extras associated with a full application. [...] When a model exists to support another model, the supporting model may be as simple as a set of classes in a separate module that address a specialty concept and provide some algorithms.

The infrastructure implements interface abstractions from the user interface, application services, and the domain model. It also dispatches operations to application services, the domain model, and the data store.

![Application](https://github.com/backpackerhh/blog-posts/assets/685978/6d49bf5e-bb1e-4ef1-b7df-989d986ef3d8)

### User interface

The user interface will often need to render properties of multiple aggregate instances. This is despite the fact that in most cases a user should be performing a state-mutating task that is to be applied to just one instance of a single type of aggregate.

A popular way to tackle the problem of rendering multiple aggregate instances to a single view is to use Data Transfer Objects (DTO). The DTO is designed to hold the entire number of attributes that need to be displayed in a view. The application service will use repositories to read the necessary aggregate instances and then delegate to a DTO assembler to map the attributes of the DTO.

The user interface component accesses each individual DTO attribute and renders it onto the view.

Both reads and writes are performed through repositories. It has the advantage of resolving any lazy-loaded collections because the DTO assembler will directly access every part of the aggregates that it needs to build the DTO.

Your aggregates will need to be designed so that DTO assemblers can query for necessary data. Think carefully about how to reveal state without revealing too much about the internal shape or structure of the aggregates. Try to eliminate a client's coupling to all internal parts of an aggregate.

To work around the problem of tight coupling between the model and its clients, you may choose to design *Mediator* (aka *Double-Dispatch* and *Callback*) interfaces to which the aggregate publishes its internal state.

Since the *Domain Payload Object* (DPO) holds references to whole aggregate instances, any lazy-loaded objects/collections are not yet resolved.

Any presentation component that references unresolved lazy-loaded objects will cause an exception.

To fix up necessary lazy loads we might choose an eager loading strategy, or we can use a *Domain Dependency Resolver*. This is a form of strategy usually employing one strategy per use case flow.

Resist the temptation to produce representations that are a one-to-one reflection of your domain model aggregate states, possibly with links to navigate to deeper state. Otherwise your clients will have to understand your domain model as well as the aggregates themselves. Clients will have to be fully aware of subtleties in behaviors and state transitions, and you will lose all benefits of abstraction.

Rather than reading multiple whole aggregate instances of various types and then programmatically composing them into a single container (DTO or DPO), you might instead use what is called a *use case optimal query*.

Design your repository with finder query methods that compose a custom object as a superset of one or more aggregate instances. The query dynamically places the results into a value object specifically designed to address the needs of the use case. You design a value object, not a DTO, because the query is domain specific, not application specific (as are DTOs). The custom use case optimal value object is then consumed directly by the view renderer.

You may design your application services to accept *Data Transformer*, where each client specifies the data transformer type. The application service would double-dispatch on the data transformer parameter, which would produce the required data format.

The presentation model acts as an adapter. It masks the details of the domain model by providing properties and behaviors that are designed in terms of the needs of the view. [...] Decisions are made in the adapter based on the state of the model as it applies to the view.

Once users have completed a task with the user interface, they will usually invoke an "apply" or "cancel" type of action, or preferably an explicit command. This will require the presentation model to reflect the user's action to the application, which in essence represents a minimal façade around an application service.

### Application services

The **application services** are the direct clients of the domain model. [...] These are responsible for task coordination of use case flows, one service method per flow. When using an ACID database, the application services also control transactions, ensuring that model state transitions are atomically persisted.

Security is also commonly cared for by application services.

Keep application services thin, using them only to coordinate tasks on the model.

If you eliminate types from the model, you avoid dependency and coupling, but you lose out on strong type checking and basic validations (guards) that you get for free from value object types. If you don't expose domain objects as return types, you will need to provide DTOs. If you provide DTOs, there may be accidental complexity in your solution from the extra overhead of the additional types. Then there is also the aforementioned memory overhead in high-traffic applications that is caused by the possibly unnecessary DTOs constantly being created and garbage collected.

You might think of a command as a DTO, but it is truly more than that. Since the command object is named for the operation that is to be carried out, it is more explicit. The command instance may be passed to an application service method.

Consider a command handler to be semantically equivalent to an application service method, but temporally decoupled.

In the case of the aggregate the domain event publisher is an aggregate output port.

Each architectural decision we make leads to positive and negative consequences.

### Composing multiple bounded contexts

With multiple *Application Layers* you would need to supply independent user interface components with each, where the user interface components would have some affinity to a specific underlying domain model. This is basically the portal-portlet style.

Since the *Application Layer* manages use cases, it may be easiest to create a single *Application Layer* as the actual source of model composition. [...] Services in that single layer are devoid of business domain logic. It will only serve to aggregate objects from each model into cohesive ones that the user interface needs.

Consider carefully where do we draw the line between composing multiple bounded contexts into a single user interface, and creating a new, clean bounded context with a unified domain model.

### Infrastructure

Wherever your infrastructure lives architecturally, it works out very well if its components depend on the interfaces from the user interface, application services, and domain model that require special technical capabilities. That way, when an application service looks up a repository, it will be dependent only on the interface from the domain model, but using the implementation from the infrastructure.

The lookup may be implicit through dependency injection or using a service factory.

Implementations of repositories are kept in the infrastructure because they deal with storage, which is not a responsibility that the model should take on. You would use the infrastructure to implement interfaces that require use of messaging, such as message queues and e-mail. If there are special user interface components that feature generated graphical charts, maps, and the like, these would also be implemented in the infrastructure.

---

And that's it. It was definitely another exhaustive journey through another great book.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]