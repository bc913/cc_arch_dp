# Modern Software Architecture


## Domain Layer

Consists of three components:
- Model: Aggregates, Entities, Value Objects, Factories
- Services: Cross-aggregate behavior, repositories, proxies to external services
- Events (optional): 

Features:
- Model side Can be defined in different ways:
    - Object Model
    - Transaction Script
    - ??
- Database agnostic
- Ubiquitous language

### Model

MAin features:
- Holds business logic
- Persistable but knows nothing about persistence


- Value Objects: 
    1. Collection of values
    2. Identified by the values of its properties/fields
    3. Immutable
    4. More precise and accurate than primitive types

```csharp
// not preferred
public int OutsideTemperature { get; set; }

// preferred
public class Temperature
{
    // ctors
    // Min-Max constants
    // Ad hoc setters with built-in validation and compensation logic
    // Ad hoc getters
    // Ad hoc comparison
    // Additional properties
}

// somewhere else
public Temperature OutsideTemperature { get; set; }

```
- Entity:
    1. Defined by its id - Uniqueness is critical
    2. Data + behavior (domain logic)
    3. Usually related with the use cases
    4. No default ctor ????
    5. Can have direct access to the properties but with only private/internal setters.


- Aggregate:
    - Collection of entities and/or value objects
    - Acts as a unit (against queries & commands) and holds an invariant. It is a transactional consistency.
    - Each contained field/property are associated with each other
    - Root: Parent of the aggregate. Public endpoint. Access to the aggregates are ONLY possible via the root so direct access to childs are not allowed. Acts as a mediator. 
    - Ask yourself if an entity can not live w/o another, then it is child. OTherwise, it can be considered as root.
    - Interaction with other aggregates is defined by the business logic.
    - Cascade updates/deletions should be achieved
    - IMPORTANT: One repository per aggregate
- Aggregate Root:
    - Define a base abstract class ( not interface - it does not let you to implement default logic)

- Model Behavior:
    - Validates the state of the objects. Satisfy the invariants.
    - Invoke business actions

### Services
- Implement domain logic which does not completely fit into one single entity or can be shared by multiple entities.
- May consume services from the infrastructure (external layers)
- They usually don't hold state, only defines behaviors.

### Impl. Examples
1. Booking a room/appointment. Can be achieved in two ways:
    a. Booking domain service: Read the member credit status - check the room availability
    b. Booking aggregate: Room and member child objects and booking repo does the job.


### Events
It consists of two parts:
1. Define them first and add to a event list
2. Raise the event
```csharp
// 1. Custom
public interface IDomainEvent
{

}
// Option 2: Use .net event mechanism
```

If the listener is in the same process, pass the raise and handle the event in the same memory space. This usually happens when the listener is another aggregate within the same bounded context.

If the listener is in another bounded context, then a contract is required between the bounded contexts to communicate. This usually happens with
- Http
- Grpc
- Message Queues

and `Service Bus` is responsible for to deliver the raised information to the listener in text (xml or json) or binary format depending on your contract.

### Anemic Models
These are models consists of data only, no behaviors aka domain models w/ behaviors.

### Best practices
- Keep domain and persistence models seperate so you need an adapter/mapper between these models to communicate. 

## Repository Pattern
```csharp
public interface IRepository<T> where T: IAggregateRoot
{
    //
}
```