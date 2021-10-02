# Persistence layer
This layer is at the same level with Infrastructure layer. Some consider this as a type of Infrastructure layer. It is usually handled using ORM. The most common one for .net is EF Core. 

The persistence layer is usually exposed using `Repository Pattern`.

## Repository Pattern
Manages the life cycle of persisted objects. The repositories internally uses ORMs to deal with the database operations. The public API should NOT expose anything to the clients. 

As other layers in the same level, they are dependent on the domain layer so they can only expose domain layer objects through their API. One of the main consumer of the persistence layer is the `Application Layer`.

Main features:
- Think of it as an in-memory collection
- One repository per aggregate
- Aggregate roots should be exposed through the APIs. This contributes to the transactional consistency.
    - i.e. when Save() or Commit() is called, the required info will be saved for aggregate root and all its childs.
- Aggregates refer each other using their ids(PK)
- Focuses on persistence
- It is a specialized version of Facade Pattern.
- Better to seperate repositories for read/write operations. It totally lies within CQRS.
    - Query focused repos can benefit from caching
    - Command focused repositories can benefit from queues.

### Implementation
- Provide a base abstract (or interface) to gather common functionality.
- Enforce the API to expose Aggregate or aggregate roots
```csharp
public interface IAggregateRoot : IEntity{}


public interface IRepository<TEntity> where TEntity :class, IAggregateRoot
{
    //
}
```
- Use different repos for aggregate roots and CRUD only data models
- Use database profiling (i.e. Azure data studio, Hibernating Rhinos, RavenDB, Azure Cosmos DB and Hibernate DB ) or logging to surface problems 
- Do not expose `IQueryable` in the API. Reasons: 
    - The client can extend the query expression which eventually ends up with additional calls to db.
    - It also causes the business query logic all over the place. It will spread out everywhere.
    - Violating Single responsibility principle and seperation of concerns
- Provide some additional methods for custom queries using predicates but this cause code bloating as more query types required.
    - It is better to use `Specification` pattern.

#### How to access child entity?
1. First call the corresponding repo method which provides an API through aggregate root.
2. Wrap it under a method
```csharp
repo.GetByChildId(int id);
```

## Specification
It is a predicate that determines if an object satisfies a criteria.
It is primaryly used for:
- Validation
- Selection & Querying
- Creation for specific purpose

Main Features:
- Works well with EF Core.

- [github.com/ardalis/Specification](github.com/ardalis/Specification)

## Best Practices
- [Avoid In-Memory Databases for tests](jimmybogard.com/avoid-in-memory-databases-for-tests/)
