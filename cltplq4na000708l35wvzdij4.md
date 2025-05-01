---
title: "The one with highlights of the blue book of DDD"
seoDescription: "Highlights of the blue book of DDD (Domain-Driven Design: Tackling Complexity in the Heart of Software) written by Eric Evans"
datePublished: Wed Mar 13 2024 09:31:19 GMT+0000 (Coordinated Universal Time)
cuid: cltplq4na000708l35wvzdij4
slug: the-one-with-highlights-of-the-blue-book-of-ddd
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1710322136656/4528c23e-dc58-470a-8046-7cc6a522d91f.png
tags: software, software-development, software-architecture, software-engineering, domain, ddd, domain-driven-design

---

This is an introduction to a new series where I'd like to highlight the most important parts of some technical books that I've read.

Usually I read in my old *Kindle Voyage* and I highlight every part of the text that I consider it's worth remembering.

Except that usually those highlights are lost in time like tears in rain, because I rarely go back to the book to look for them as it's easier to do a quick search on the internet.

And that's exactly why I want to have a blog post where those highlights could be more accessible.

> First and foremost I would like to emphasize my greatest admiration for any of the books and authors I will include in this series.

Let's start with a book by **Eric Evans** that changed the way we write code more than 20 years ago: [Domain-Driven Design: Tackling Complexity in the Heart of Software](https://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215).

---

## I. Putting the domain model to work

The heart of software is its ability to solve domain-related problems for its user.

Developers have to steep themselves in the domain to build up knowledge of the business.

Leaders within a team who understand the centrality of the domain can put their software project back on course when development of a model that reflects deep understanding gets lost in the shuffle.

### Crunching knowledge

A team of developers and domain experts collaborate, typically led by developers. Together they draw in information and crunch it into a useful form. The raw material comes from the minds of domain experts, from users of existing systems, from the prior experience of the technical team with a related legacy system or another project in the same domain.

The constant refinement of the domain model forces the developers to learn the important principles of the business they are assisting, rather than to produce functions mechanically.

The domain experts often refine their own understanding by being forced to distill what they know to essentials, and they come to understand the conceptual rigor that software projects require.

Knowledge crunching is an exploration, and you can't know where you will end up.

### Communication and the use of language

A model-based language should be used among developers to describe not only artifacts in the system, but tasks and functionality.

This same model should supply the language for the developers and domain experts to communicate with each other, and for the domain experts to communicate among themselves about requirements, development planning, and features.

Even the same person uses different language in speech and in writing, so that the most incisive expressions of the domain often emerge in a transient form that is never captured in the code or even in writing.

Persistent use of the **ubiquitous language** will force the model's weaknesses into the open. The team will experiment and find alternatives to awkward terms or combinations. As gaps are found in the language, new words will enter the discussion. These changes to the language will be recognized as changes in the domain model and will lead the team to update class diagrams and rename classes and methods in the code, or even change behavior, when the meaning of a term changes.

The developers and domain experts can informally test the model by walking through scenarios, using the model objects step-by-step. Almost every discussion is an opportunity for the developers and user experts to play with the model together, deepening each other's understanding and refining concepts as they go.

The domain experts can use the language of the model in writing use cases, and can work even more directly with the model by specifying acceptance tests.

Well-written code can be very communicative, but the message it communicates is not guaranteed to be accurate. [...] It takes fastidiousness to write code that doesn't just do the right thing but also says the right thing.

### Binding model and implementation

Tightly relating the code to an underlying model gives the code meaning and makes the model relevant.

An *analysis model* is the product of analyzing the business domain to organize its concepts without any consideration of the part it will play in a software system. An analysis model is meant as a tool for understanding only; mixing in implementation concerns is thought to muddy the waters.

If the managers perceive analysis to be a separate process, the development team may not be given adequate access to domain experts.

**Model-Driven Design** discards the dichotomy of analysis model and design to search out a single model that serves both purposes.

When a model doesn't seem to be practical for implementation, we must search for a new one. When a model doesn't faithfully express the key concepts of the domain, we must search for a new one. The modeling and design process then becomes a single iterative loop.

Although many developers benefit from just applying the technical capabilities of objects to organize program code, the real breakthrough of object design comes when the code expresses the concepts of a model.

If the people who write the code do not feel responsible for the model, or don't understand how to make the model work for an application, then the model has nothing to do with the software.

Any technical person contributing to the model must spend some time touching the code, whatever primary role they play on the project.

## II. The building blocks of a Model-Driven Design

Developing a good domain model is an art.

![The building blocks of a Model-Driven Design](https://github.com/backpackerhh/blog-posts/assets/685978/78e276ad-dd30-4034-8d71-63e0e3a34ddd)

### Isolating the domain

The best architectural frameworks solve complex technical problems while allowing the domain developer to concentrate on expressing a model.

When applying a framework, the team needs to focus on its goal: building an implementation that expresses a domain model and uses it to solve important problems. The team must seek ways of employing the framework to those ends, even if it means not using all of the framework's features.

A lot of the downside of frameworks can be avoided by applying them selectively to solve difficult problems without looking for a one-size-fits-all solution.

Isolating the domain implementation is a prerequisite for **Domain-Driven Design** that pays off best for ambitious projects, and it does require strong skills.

Domain and UI should be separate.

### A model expressed in software

#### Associations

It is important to constrain relationships as much as possible. A bidirectional association means that both objects can be understood only together. When application requirements do not call for traversal in both directions, adding a traversal direction reduces interdependence and simplifies the design. Understanding the domain may reveal a natural directional bias.

The ultimate simplification is to eliminate an association altogether, if it is not essential to the job at hand or the fundamental meaning of the model objects.

#### Entities (a.k.a. reference objects)

An object defined primarily by its identity is called an **entity**.

An entity is anything that has continuity through a life cycle and distinctions independent of attributes that are important to the application's user.

Each entity must have an operational way of establishing its identity with another object, distinguishable even from another object with the same descriptive attributes.

An identifying attribute must be guaranteed to be unique within the system however that system is defined, even if distributed or when objects are archived.

Sometimes certain data attributes, or combinations of attributes, can be guaranteed or simply constrained to be unique within the system. This approach provides a unique key for the entity.

When the application requires an external ID, the users of the system become responsible for supplying IDs that are unique, and the system must give them adequate tools to handle exceptions that arise.

#### Value objects

An object that represents a descriptive aspect of the domain with no conceptual identity is called a **value object**.

Value objects are instantiated to represent elements of the design that we care about only for what they are, not who or which they are.

A value object can be an assemblage of other objects and can even reference entities.

Value objects are often passed as parameters in messages between objects. They are frequently transient, created for an operation and then discarded and are used as attributes of entities.

The attributes that make up a value object should form a conceptual whole.

Treat the value object as immutable [...] it cannot be changed except by full replacement.

Developers are free to make decisions about issues such as copying and sharing on a purely technical basis, secure in the knowledge that the application does not rely on particular instances of the objects.

#### Services

There are important domain operations that can't find a natural home in an entity or value object. Some of these are intrinsically activities or actions, not things, but since our modeling paradigm is objects, we try to fit them into objects anyway.

The more common mistake is to give up too easily on fitting the behavior into an appropriate object, gradually slipping toward procedural programming.

A **service** is an operation offered as an interface that stands alone in the model, without encapsulating state, as entities and value objects do.

A service tends to be named for an activity, rather than an entity - a verb rather than a noun.

A good service has 3 characteristics:

1. The operation relates to a domain concept that is not a natural part of an entity or value object.
2. The interface is defined in terms of other elements of the domain model.
3. The operation is stateless.

Define the interface making sure the operation name is part of the ubiquitous language.

Many domain or application services are built on top of the populations of entities and values, behaving like scripts that organize the potential of the domain to actually get something done.

#### Modules (a.k.a. packages)

Cognitive overload is the primary motivation for modularity.

There should be low coupling between **modules** and high cohesion within them.

Well-chosen modules bring together elements of the model with particularly rich conceptual relationships.

Letting the modules reflect changing understanding of the domain will also allow more freedom for the objects within them to evolve.

Modules are a communications mechanism.

When you place some classes together in a module, you are telling the next developer who looks at your design to think about them together.

Give the modules names that become part of the ubiquitous language.

Modules need to coevolve with the rest of the model. This means refactoring modules right along with the model and code. But this refactoring often doesn't happen. Changing modules tends to require widespread updates to the code.

Inevitable early mistakes in module choices lead to high coupling, which makes it hard to refactor. The lack of refactoring just keeps increasing the inertia.

Unless there is a real intention to distribute code on different servers, keep all the code that implements a single conceptual object in the same module, if not the same object.

### The life cycle of a domain object

![The life cycle of a domain object](https://github.com/backpackerhh/blog-posts/assets/685978/41e4bcb6-e503-4e60-99a9-aa71e04005e9)

#### Aggregates

An **aggregate** is a cluster of associated objects that we treat as a unit for the purpose of data changes. Each aggregate has a *root* and a *boundary*.

The boundary defines what is inside the aggregate. The root is a single, specific entity contained in the aggregate.

Invariants, which are consistency rules that must be maintained whenever data changes, will involve relationships between members of the aggregate. Any rule that spans aggregates will not be expected to be up-to-date at all times.

The invariants applied within an aggregate will be enforced with the completion of each transaction.

Rules to apply to all transactions:

* The root entity has global identity and is ultimately responsible for checking invariants.
* Entities inside the boundary have local identity, unique only within the aggregate.
* Nothing outside the aggregate boundary can hold a reference to anything inside, except to the root entity. The root may hand a copy of a value object to another object, and it doesn't matter what happens to it, because it's just a value and no longer will have any association with the aggregate.
* Only aggregate roots can be obtained directly with database queries. All other objects must be found by traversal of associations.
* Objects within the aggregate can hold references to other aggregate roots.
* A delete operation must remove everything within the aggregate boundary at once.
* When a change to any object within the aggregate boundary is committed, all invariants of the whole aggregate must be satisfied.

#### Factories

Complex object creation is a responsibility of the domain layer, yet that task does not belong to the objects that express the model.

A **factory** encapsulates the knowledge needed to create a complex object or aggregate. It provides an interface that reflects the goals of the client and an abstract view of the created object.

The two basic requirements for any good factory:

1. Each creation method is atomic and enforces all invariants of the created object or aggregate. A factory should only be able to produce an object in a consistent state.
2. The factory should be abstracted to the type desired, rather than the concrete class(es) created.

Avoid calling constructors within constructors of other classes. Constructors should be dead simple.

*Entity factories* tend to take just the essential attributes required to make a valid aggregate. Details can be added later if they are not required by an invariant.

A factory used for reconstitution is very similar to one used for creation, with two major differences:

1. An entity factory used for reconstitution does not assign a new tracking ID.
2. A factory reconstituting an object will handle violation of an invariant differently. During creation of a new object, a factory should simply balk when an invariant isn't met, but a more flexible response may be necessary in reconstitution.

To be consistent, consider adopting a coding standard for failures in factories.

#### Repositories

Clients request objects from a **repository** using query methods that select objects based on specified criteria, typically the value of certain attributes. The repository retrieves the requested object, encapsulating the machinery of database queries and metadata mapping.

For each type of object that needs global access, create an object that can provide the illusion of an in-memory collection of all objects of that type. Set up access through a well-known global interface.

Provide methods to add and remove objects, which will encapsulate the actual insertion or removal of data in the data store.

They can also return summary information, such as a count of how many instances meet some criteria. They can even return summary calculations, such as the total across all matching objects of some numerical attribute.

Provide repositories only for aggregate roots that actually need direct access.

Even a repository design with flexible queries should allow for the addition of specialized hard-coded queries.

In general, don't fight your frameworks. Seek ways to keep the fundamentals of Domain-Driven Design and let go of the specifics when the framework is antagonistic. [...] This is assuming that you have no choice but to use the framework.

The factory makes new objects, the repository finds old objects.

A repository uses a factory to reconstitute a preexisting object.

## III. Refactoring toward deeper insight

Initial models usually are naive and superficial, based on shallow knowledge.

A **deep model** provides a lucid expression of the primary concerns of the domain experts and their most relevant knowledge while it sloughs off the superficial aspects of the domain.

To create a design really fitted to the problem at hand, you must first have a model that captures the central relevant concepts of the domain.

Deep models can emerge gradually through a sequence of small refactorings, an object at a time.

### Making implicit concepts explicit

When the users or domain experts use vocabulary that is nowhere in the design, that is a warning sign.

Different domain experts see things different ways based on their experience and needs. Even the same person provides information that is logically inconsistent after careful analysis. Such pesky contradictions, which we encounter all the time when digging into program requirements, can be great clues to deeper models. Some are just variations in terminology or are based on misunderstanding.

Factoring a constraint into its own method allows us to give it an intention-revealing name that makes the constraint explicit in our design.

A **specification** is a predicate that determines if an object does or does not satisfy some criteria.

### Supple design

![Supple design](https://github.com/backpackerhh/blog-posts/assets/685978/503fd38d-c247-46e8-ac1e-fb68c4dcf4ab)

**Supple design** is the complement to *deep modeling*.

If someone other than the original developer must infer the purpose of an object or operation based on its implementation, that new developer may infer a purpose that the operation or class fulfills only by chance. If that was not the intent, the code may work for the moment, but the conceptual basis of the design will have been corrupted, and the two developers will be working at cross-purposes.

The names of classes and methods are great opportunities for improving communication between developers, and for improving the abstraction of the system.

Type names, method names, and argument names all combine to form an **intention-revealing interface**.

You can keep the commands and queries strictly segregated in different operations. Ensure that the methods that cause changes do not return domain data and are kept as simple as possible. Perform all queries and calculations in methods that cause no observable side effects.

An operation that mixes logic or calculations with state change should be refactored into two separate operations.

By definition, this segregation of side effects into simple command methods only applies to entities.

The side effect often can be completely eliminated by deriving a value object instead of changing existing state, or by moving the entire responsibility into a value object.

**Assertions** make side effects explicit and easier to deal with.

Clearly stated invariants and pre- and post-conditions allow a developer to understand the consequences of using an operation or object.

The communicativeness of the intention-revealing interfaces, combined with the predictability given by **side-effect-free functions** and assertions, should make encapsulation and abstraction safe.

Repeated refactoring eventually leads to suppleness. The **conceptual contours** emerge as the code is adapted to newly understood concepts or requirements.

Each object should be a single complete concept, a **whole value**.

The goal is a simple set of interfaces that combine logically to make sensible statements in the ubiquitous language, and without the distraction and maintenance burden of irrelevant options.

When successive refactoring tends to be localized, not shaking multiple broad concepts of the model, it is an indicator of model fit.

Encountering a requirement that forces extensive changes in the breakdown of the objects and methods is a message: Our understanding of the domain needs refinement.

Low coupling is a basic way to reduce conceptual overload. A **standalone class** is an extreme of low coupling.

Eliminating dependencies should not mean dumbing down the model by arbitrarily reducing everything to primitives. The goal is to eliminate all nonessential dependencies.

Logical operations are closed under predicates, so specification combinations will exhibit **closure of operations**.

### Applying analysis patterns

**Analysis patterns** are groups of concepts that represent a common construction in business modeling. It may be relevant to only one domain or it may span many domains.

If your model definitions change through the natural evolution of the model, take the trouble to change the names too.

### Refactoring toward deeper insight

Seeking insight into the domain creates a broader context for the process of refactoring.

Whatever the source of dissatisfaction, the next step is to seek a refinement that will make the model communicate clearly and naturally.

If you wait until you can make a complete justification for a change, you've waited too long.

Don't be absolute about things, but push beyond the comfort zone in the direction of favoring refactoring.

Refactoring toward deeper insight is a continuing process. Implicit concepts are recognized and made explicit. Parts of the design are made suppler, perhaps taking on a declarative style.

## IV. Strategic design

Strategic design principles must guide design decisions to reduce the interdependence of parts and improve clarity without losing critical interoperability and synergy.

### Maintaining model integrity

![Strategic design](https://github.com/backpackerhh/blog-posts/assets/685978/89823fd4-e4e8-420a-ae86-e09b249fa8da)

The internal consistency of a model, such that each term is unambiguous and no rules contradict, is called *unification*.

Total unification of the domain model for a large system will not be feasible or cost-effective.

#### Bounded context

A **bounded context** delimits the applicability of a particular model so that team members have a clear and shared understanding of what has to be consistent and how it relates to other contexts.

Within that context, work to keep the model logically unified, but do not worry about applicability outside those bounds. In other contexts, other models apply, with differences in terminology, in concepts and rules, and in dialects of the ubiquitous language.

Integration across the boundaries necessarily will involve some translation, which you can analyze explicitly.

The separate name spaces that modules create within a bounded context actually make it harder to spot accidental model fragmentation.

#### Continuous integration

**Continuous integration** means that all work within the context is being merged and made consistent frequently enough that when splinters happen they are caught and corrected quickly.

In a Model-Driven Design, the integration of concepts smooths the way for the integration of the implementation, while the integration of the implementation proves the validity and consistency of the model and exposes splinters.

Continuous integration is essential only within a bounded context. Design issues involving neighboring contexts, including translation, don't have to be dealt with at the same pace.

#### Context map

![Context map](https://github.com/backpackerhh/blog-posts/assets/685978/a05a8b32-0c05-4dd0-a915-7014028c6369)

Code reuse between bounded contexts is a hazard to be avoided. Integration of functionality and data must go through a translation.

People who work closely will naturally share a model context. People on different teams, or those that don't talk, even if they are on the same team, will split off into different contexts.

Once you have a coherent **context map**, you'll see things you want to change. You can make considered changes to the organization of teams or to the design. Remember, don't change the map until the change in reality is done.

Model contexts always exist, but without conscious attention they may overlap and fluctuate. By explicitly defining bounded contexts and a context map, your team can begin to direct the process of unifying models and connecting distinct ones.

Contact points with other bounded contexts are particularly important to test.

#### Shared kernel

![Shared kernel](https://github.com/backpackerhh/blog-posts/assets/685978/87e48544-946c-441d-adf6-d78b8943be66)

The **shared kernel** cannot be changed as freely as other parts of the design. Decisions involve consultation with another team. Automated test suites must be integrated because all tests of both teams must pass when changes are made.

The goal is to reduce duplication (but not to eliminate it, as would be the case if there were just one bounded context) and make integration between the two subsystems relatively easy.

#### Customer/supplier development teams

Upstream and downstream subsystems separate naturally into two bounded contexts.

Downstream needs things from upstream, but upstream is not responsible for downstream deliverables.

In planning sessions, make the downstream team play the customer role to the upstream team. Negotiate and budget tasks for downstream requirements so that everyone understands the commitment and schedule.

Jointly develop automated acceptance tests that will validate the interface expected. Add these tests to the upstream team's test suite, to be run as part of its continuous integration. This testing will free the upstream team to make changes without fear of side effects downstream.

**Customer/supplier teams** are more likely to succeed if the two teams work under the same management, so that ultimately they do share goals, or where they are in different companies that actually have those roles.

#### Conformist

When two development teams have an upstream/downstream relationship in which the upstream has no motivation to provide the downstream team's needs, the downstream team is helpless. Altruism may motivate upstream developers to make promises, but they are unlikely to be fulfilled.

Where the shared kernel is a collaboration between two teams that coordinate tightly, **conformist** deals with integration with a team that is not interested in collaboration.

#### Anticorruption layer

![Anticorruption layer](https://github.com/backpackerhh/blog-posts/assets/685978/e46f45a0-3c26-4a4b-b261-7d6c61b9ce1e)

The public interface of the **anticorruption layer** usually appears as a set of services, although occasionally it can take the form of an entity.

Building a whole new layer responsible for the translation between the semantics of the two systems gives us an opportunity to reabstract the other system's behavior and offer its services and information to our system consistently with our model.

A **façade** is an alternative interface for a subsystem that simplifies access for the client and makes the subsystem easier to use.

The façade belongs in the bounded context of the other system.

For each service we define, we need an **adapter** that supports the service's interface and knows how to make equivalent requests of the other system or its façade.

An anticorruption layer can be bidirectional, defining services on both interfaces with their own adapters, potentially using the same translators with symmetrical translations.

Functionality can be added to the anticorruption layer if it is specific to the relationship of the two subsystems.

#### Separate ways

Declare a bounded context to have no connection to the others at all, allowing developers to find simple, specialized solutions within this small scope.

#### Open host service

Define a protocol that gives access to your subsystem as a set of services. Open the protocol so that all who need to integrate with you can use it. Enhance and expand the protocol to handle new integration requirements, except when a single team has idiosyncratic needs. Then, use a one-off translator to augment the protocol for that special case so that the shared protocol can stay simple and coherent.

#### Published language

Use a well-documented shared language that can express the necessary domain information as a common medium of communication, translating as necessary into and out of that language.

By explicitly defining a context within which each model applies, you can maintain the integrity of each and clearly see the implications of any particular interface you want to create between the two.

#### Choosing your model context strategy

Teams have to make decisions about where to define bounded contexts and what sort of relationships to have between them.

Favoring larger bounded contexts:

* Flow between user tasks is smoother when more is handled with a unified model.
* It is easier to understand one coherent model than two distinct ones plus mappings.
* Translation between two models can be difficult (sometimes impossible).
* Shared language fosters clear team communication.

Favoring smaller bounded contexts:

* Communication overhead between developers is reduced.
* Continuous integration is easier with smaller teams and code bases.
* Larger contexts may call for more versatile abstract models, requiring skills that are in short supply.
* Different models can cater to special needs or encompass the jargon of specialized groups of users, along with specialized dialects of the ubiquitous language.

![The relative demands of CONTEXT relationship patterns](https://github.com/backpackerhh/blog-posts/assets/685978/ab6b0e58-2913-4533-9e7d-aa7eaffd77df)

Generally speaking, there is a correspondence of one team per bounded context. One team can maintain multiple bounded contexts, but it is hard (though not impossible) for multiple teams to work on one together.

Do not equate the interchange language and the model of the host. Keeping them close together will reduce translation overhead, and you may choose to make your host a conformist. But reserve the right to beef up the translation layer and diverge if the cost-benefit trade-off favors that.

### Distillation

A model is a distillation of knowledge.

![Distillation](https://github.com/backpackerhh/blog-posts/assets/685978/34939bb1-4051-4cb7-b04b-f3e79405acf8)

Distillation does not operate only on the gross level of separating parts of the domain away from the core. It also means refining those subdomains, especially the core domain, through continuously refactoring toward deeper insight, driving toward a deep model and supple design.

Although a breakthrough to a deep model provides value anywhere it happens, it is in the core domain that it can change the trajectory of an entire project.

#### Core domain

A system that is hard to understand is hard to change. The effect of a change is hard to foresee.

The planning process must drive resources to the most crucial points in the model and design. To do that, those points must be identified and understood by everyone during planning and development.

One application's **core domain** is another application's generic supporting component.

The identification of the core domain should evolve through iterations.

The greatest value of custom software comes from the total control of the core domain.

Creating distinctive software comes back to a stable team accumulating specialized knowledge and crunching it into a rich model. No shortcuts. No magic bullets.

#### Generic subdomains

Identify cohesive subdomains that are not the motivation for your project. Factor out generic models of these subdomains and place them in separate *modules*.

Generic doesn't mean reusable.

You should be applying as much of your effort to the core domain as possible and investing in supporting **generic subdomains** only as necessary.

Industry-specific concepts belong either in the core domain or in their own, more specialized, subdomains, and those specialized models are even more valuable than the generic ones.

#### Highlighted core

Create a separate document to describe and explain the core domain. It can be as simple as a list of the most essential conceptual objects. It can be a set of diagrams focused on those objects, showing their most critical relationships.

A distillation document is not a complete design document.

If the distillation document outlines the essentials of the core domain, then it serves as a practical indicator of the significance of a model change. When a model or code change affects the distillation document, it requires consultation with other team members. When the change is made, it requires immediate notification of all team members, and the dissemination of a new version of the document.

#### Cohesive mechanisms

The model of the core domain or a generic subdomain formulates a fact, rule, or problem. A **cohesive mechanism** resolves the rule or completes the computation as specified by the model.

A model proposes; a cohesive mechanism disposes.

You almost always want to remove mechanisms from the core domain. The one exception is when a mechanism is itself proprietary and a key part of the value of the software.

#### Segregated core

Refactor the model to separate the core concepts from supporting players (including ill-defined ones) and strengthen the cohesion of the core while reducing its coupling to other code. Factor all generic or supporting elements into other objects and place them into other packages, even if this means refactoring the model in ways that separate highly coupled elements.

Because the core domain evolves just like every other aspect of a design, experience working with a **segregated core** will lead to new insights into what is essential and what is a supporting element.

#### Abstract core

If most of the interactions across modules can be expressed at the level of polymorphic interfaces, it may make sense to refactor these types into a special **core module**.

### Large-scale structure

![Large-scale structure](https://github.com/backpackerhh/blog-posts/assets/685978/e6569c79-3f5a-4c57-826a-a04216ed3aa9)

The strict segregation imposed by bounded contexts prevents corruption and confusion, but it does not, in itself, make it easier to see the system as a whole.

Distillation does help by focusing attention on the core domain and casting other subdomains in their supporting roles. But it is still necessary to understand the supporting elements and their relationships to the core domain; and to each other.

We need to be able to understand the role of an individual part in the whole without delving into the details of the whole.

#### Evolving order

Architectures can straitjacket a project with up-front design assumptions and take too much power away from the developers/designers of particular parts of the application.

The problem is not the existence of guiding rules, but rather the rigidity and source of those rules. If the rules governing the design really fit the circumstances, they will not get in the way but actually push development in a helpful direction, as well as provide consistency.

Choosing to use a large-scale structure favors manageability of the model as a whole over optimal structuring of the individual parts.

A large-scale structure generally needs to be applicable across bounded contexts.

It has to leave freedom for development teams in distinct contexts to vary the model in ways that address their local needs.

#### System metaphor

**System metaphor** is a well-known form of large-scale structure that is useful on some projects, and it nicely illustrates the general concept of a structure.

The system metaphor should both facilitate communication about the system and guide development of it. This increases consistency in different parts of the system, potentially even across different bounded contexts. But because all metaphors are inexact, continually reexamine the metaphor for overextension or inaptness, and be ready to drop it if it gets in the way.

#### Responsibility layers

If everyone on a project makes decisions in a consistent way, the design as a whole will be much more comprehensible, and that is worth some modest trade-offs on detailed design choices.

The concepts in the "upper" layers should have meaning against the backdrop of the "lower" layers, while the lower-layer concepts should be meaningful standing alone.

#### Knowledge level

A **knowledge level** is a group of objects that describes how another group of objects should behave.

Create a distinct set of objects that can be used to describe and constrain the structure and behavior of the basic model. Keep these concerns separate as two "levels", one very concrete, the other reflecting rules and knowledge that a user or superuser is able to customize.

If the knowledge level becomes complex, the system's behavior becomes hard to understand for developers and users alike.

#### Pluggable component framework

High-level abstractions are identified and shared across the breadth of the system; specialization occurs in modules.

#### Refactoring toward a fitting structure

Your final structure will not be available at the start, and that means that you will have to refactor to impose it as you go along. This can be expensive and difficult, but it is necessary.

Without consistent adherence by the many people involved, structures have a tendency to decay.

The kinds of conversations that take place on most teams are not enough to maintain a consistent large-scale structure in a system. It is critical to incorporate it into the ubiquitous language of the project, and for everyone to exercise that language relentlessly.

The principles of distillation and refactoring toward deeper insight apply even to the large-scale structure itself.

### Bringing the strategy together

![The three basic principles of strategic design](https://github.com/backpackerhh/blog-posts/assets/685978/ad10f40a-3a80-44de-9e42-378ccdeefcc0)

The three basic principles of strategic design (context, distillation, and large-scale structure) are not substitutes for each other; they are complementary and interact in many ways.

The large-scale structure can help explain the relationships within the core domain and between generic subdomains.

Distinguishing the layering of potential, operations, policy, and decision support distills an insight that is fundamental to the business problem addressed by the software. This insight is especially useful if a project is carved up into many bounded contexts, so that the model objects of the core domain don't have meaning over much of the project.

When a large-scale structure spans multiple teams, closely affiliated teams may begin to collaborate informally. In such a situation, each application team still makes the discoveries that lead to the idea for a large-scale structure, but then particular options are discussed by the informal committee, made up of representatives of the various teams. After assessing the impact of the design, participants may decide to adopt it, modify it, or leave it on the table.

Creating an organizing principle, large-scale structure, or distillation of such subtlety requires a really deep understanding of the needs of the project and the concepts of the domain. The only people who have that depth of knowledge are the members of the application development team. This explains why application architectures created by architecture teams are so seldom helpful, despite the undeniable talent of many of the architects.

It is essential to have strong designers on all application teams. It is essential to have domain knowledge on any team attempting strategic design. It may simply be necessary to hire more advanced designers. It may help to keep architecture teams part-time.

Any effective strategy team has to have as a partner an effective application team.

Realizing that your best idea is likely to get in somebody's way takes humility.

Strategic design emerges out of application design, yet it requires a big-picture view of activity, possibly spanning multiple teams.

There is a risk that an architecture can interfere with expressive implementations of the domain model and easy change.

---

And that's it. It was definitely an exhaustive journey through this great book.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]