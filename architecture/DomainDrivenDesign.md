# Domain Driven Design
github.com/ardalis/pluralsight-ddd-fundamentals
## Domain Layer
- Entity: A domain class with an identity for tracking, locate retrieve and store. The identity does not tie to the property values.

Entities do not support dependency injection through ctor. Only allowed through methods.

- Value Object:
- Aggregate: 


Class that affect state are considered as anti-pattern in a domain model. This is Anemic Domain model.

Anemic Domain Model: Focus on the state of the object. This is acceptable if you only deal with CRUD operations. 

Rich Domain Model: If it is not simple enough to describe enough with CRUD. Represents Behaviorus and business logic.

- The properties(fields) should NOT basically allow for setting. It should be in valid state so using ctor with some checks is better practice. Make the properties readonly.
- They might not be IMMUTABLE so we should provide setters through methods like C++. Additional logic might be added here.

Two types of objects:
- Defined by identity: Entity
- Defined by its values: Value objects (immutable)
 identity is based on composition of values
 no side effects
 compared with all values


Contexts with ONLY simple CRUD functionality:
- Entities derived from `BaseEntity<int>` not `BaseEntity<Guid>`. Let the database assign (generate) their Ids. All the management is done using CRUD.
- Database performance favors int for keys
- If these entites have also live within another bounded contexts, define them as readonly. because they are managed in other context.

Contexts with Richer Domain
- Entities are generated within the domain with its logic not relying to the database so using GUIDs are better practice.
- Since it totally lies within the DDD logic: Domain entities are created w/o using databases.

## Value objects
- Should have no side effects
- Strings and dates
- They are identified by the combination  of their property values.
- They are used as a property of an entity
- Date libraries as value objects
- Wrap ids into value objects 
- Value objects should get their state through ctor and satisfy the invariant in the ctor.

## Domain Services
If you can't find an entity or value object to place some behaviour or logic, you can define stateless services within the domain called `services`. It can be considered as free functions in C++.

IF your rich model tends to be anemic model.

## Aggregates
Transactional graph of objects
ctors are responsible to satisfy ttbe invariants

consist of one or more entity and value objects that change together. Treat them as unit for data changes and need to consider entire aggregate consistency before applying changes.

Aggregateroot: Every aggregate must have a root which is the Parent object of the all members of the aggregate.

Data changes should be A(tommic), C(onsistent), I(solated) and D(urable)/

Cluster of assocaited object considered as unit against data changes.

First consider to implement one way (unidirectional) or one to many relationship
- Appointment -> client, appointment->Doctor
- Client->->-> Patient


Consider bidirectional relationship only when both end can not live w/o each other.

> External objects should only interact with the aggregate root. (i.e. Order - Client's addreess)

Remove the navigation property and use the foewign key id to save orm confusion and avoid bidirectional relationship. i,e. when address is updated, the client data can also be updated in the database even removed if not doing the advised.

Client [root] (Name and Patients)
Patient (Non - Root) holds clientId

> TEST: if deleting an aggregate object causes other to delete also, then it is the root. For this reason, removing an appointment do not remove the patients and clients.

Aggregate roots are responsible to satisfy the invariants.

## Repositories
- Use different repos for aggregate roots and CRUD only data models
- Repo focuses on persistence
- Use database profiling (i.e. Azure data studio, Hibernating Rhinos, RavenDB, Azure Cosmos DB and Hibernate DB ) or logging to surface problems 
- Use/Pass predicates or specifications(preferred) in repos



## Specification
github.com/ardalis/Specification

Avoid In-Memory Databases for tests
jimmybogard.com/avoid-in-memory-databases-for-tests/

## Domain Events
- To capture an occurence of something that happened in the domain
- Represent the past
- Typically immutable

1. Create interface or base types for Handlers & Domain event
2. Define Event
3. Create Handlers for each event
4. Register the events on the associated entity

ardalis/domaineventsconsole
ardalis/CleanArchitecture [starting template]

## Anti-Corruption Layer
- Translate between foreign systems (bounded context)' models and your own
- Simplifies communication between systems
- MAy employ design patterns such as facade or adapter

[Lost in bounded context tranlations with Julie, Idnu, Micheal and Nick](youtu.be/u-5sKvh48-g)


## Shared 
### Eventual consistency
When two or more syteems or bounded contexts do not need to be synced immediately but the changes will eventually get to their destination.

Ways :
- Publisher/subscriber

## Microservices
Bounded Contexts, Microservices and Everything in bewtween - Vladik Khononov
## References
- [](domainlanguage.com)
- [Layers, Onions, Ports, Adapters: it's all the same by Mark Seemann](https://blog.ploeh.dk/2013/12/03/layers-onions-ports-adapters-its-all-the-same/)
- 
- https://github.com/matthewrenze/clean-architecture-demo

- [Gerg Young](https://goodenoughsoftware.net)

- [Udi Dahan](http://udidahan.com)

- [Vaugh Vernon](vaughvernon.com)

https://eventmodeling.org/

https://www.eventstorming.com/

miro.com