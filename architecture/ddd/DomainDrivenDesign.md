# Domain Driven Design
github.com/ardalis/pluralsight-ddd-fundamentals
- Domain Layer
- Infrastructure
- Shared Kernel
- Persistence
- Communication between Bounded Contexts


## Repositories
- Use different repos for aggregate roots and CRUD only data models
- Repo focuses on persistence
- Use database profiling (i.e. Azure data studio, Hibernating Rhinos, RavenDB, Azure Cosmos DB and Hibernate DB ) or logging to surface problems 
- Use/Pass predicates or specifications(preferred) in repos

## Specification
github.com/ardalis/Specification

Avoid In-Memory Databases for tests
jimmybogard.com/avoid-in-memory-databases-for-tests/

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
- [Domain-Driven Design Fundamentals by Julie Lerman & Steve Smith]

### Domain Layer

- [Services in DDD - Jimmy Bogard](bit.ly/1ifravE)



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