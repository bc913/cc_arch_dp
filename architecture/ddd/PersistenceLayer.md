# Persistence layer
This layer is at the same level with Infrastructure layer. Some consider this as a type of Infrastructure layer. It is usually handled using ORM. The most common one for .net is EF Core. 

The persistence layer is usually exposed using `Repository Pattern`.

## Repository Pattern
The repositories internally uses ORMs to deal with the database operations. The public API should NOT expose anything to the clients.

As other layers in the same level, they are dependent on the domain layer so they can only expose domain layer objects through their API. One of the main consumer of the persistence layer is the `Application Layer`.

Main features:
- One repository per aggregate
- Aggregate roots should be exposed through the APIs. This contributes to the transactional consistency.
    - i.e. when Save() or Commit() is called, the required info will be saved for aggregate root and all its childs.
- Aggregates refer each other using their ids(PK)
- Focuses on persistence

### Implementation
- Provide a base abstract (or interface) to gather common functionality.
- Enforce the API to expose Aggregate or aggregate roots
```csharp
public interface IRepository<T> where T: IAggregateRoot
{
    //
}
```
- Use different repos for aggregate roots and CRUD only data models
- Use database profiling (i.e. Azure data studio, Hibernating Rhinos, RavenDB, Azure Cosmos DB and Hibernate DB ) or logging to surface problems 
- Use/Pass predicates or specifications(preferred) in repos

#### How to access child entity?
1. First call the corresponding repo method which provides an API through aggregate root.
2. Wrap it under a method
```csharp
repo.GetByChildId(int id);
```

## Specification
- [github.com/ardalis/Specification](github.com/ardalis/Specification)

## Best Practices
- [Avoid In-Memory Databases for tests](jimmybogard.com/avoid-in-memory-databases-for-tests/)
