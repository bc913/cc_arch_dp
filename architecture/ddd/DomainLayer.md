# Domain Layer
It represents the business logic with its rules, concepts and information. Domain does not focus on data but also behaviors. 

Elements of the domain layer
- Domain Model:
    - Value Objects
    - Entity
- Domain Services
- Aggregates
- Domain Events

## Domain Model
This is the core of the business logic. The core implementation of the business logic lives here. There are three types of Domain Models:

- Defined by an identity (Entity)
- Defined by its values (Value Objects)
- Aggregates

Main Features:
- Database agnostic (Persistable but knows nothing about persistence)

These domain models can be implemented in two ways (Depending on the business logic):

1. Anemic (CRUD way): It only focuses on the state (attribute) of the object. This is quite acceptable if you only deal with CRUD operations. There is nothing wrong with this. It is still usable within DDD. It has little or no behavior. Property bags of getters or setters. The behaviors are shifted to the domain services which sits top of the domain model.

> [Martin Fowler](martinfowler.com/bliki/AnemicDomainModel.html) The fundamental horror of this anti-pattern is that it's so contrary to the basic idea of object-oriented design; which is to combine data and process together.

2. Rich (DDD Way): It combines the data with behaviors. This type of model can better represent the business model when the business logic is complex.

### **Entity**
It is a domain class with an identity for tracking, locate, retrieve and store. Its main features are:
- It is defined by its identity. Should be unique throughout the application.
- The identity does not tie to the property values.
- Mutable
- Usually associated with the use-case approach.

#### Implementation details

*Common rules*:
- There should only be one record to represent a particular entity in a bounded context.
- It should definitely lies within the `Single Responsibility Principle`. It's main responsibility is identity and business logic.
- Usually requires a (private) default ctor for ORM compatibility such as EF Core.
- Make the fields read-only and set/update the fields through methods.

```csharp
public abstract class BaseEntity<IdType>
{
    public IdType Id { get; set; }
    public List<BaseDomainEvent> Events = new List<BaseDomainEvent>();
}
```
*Anemic Model*: 

Since they are representing basic CRUD logic ONLY, we let the persistence logic to generate its ids so keeping their ids as `int`. It also has database performance benefits.

They usually expose their properties with simple public getters and setters. Also, no business specific behavior ( method) requires to be implemented. This also means that they don't need to satisfy an invariant.

```csharp
public class Client : BaseEntity<int>, IAggregateRoot{
    public string Name { get; set; }
    public string LastName { get; set;}
}
// or
public class Patient : BaseEntity<int>
{

}
```
If these entites have also to live within another bounded contexts, define them as readonly because they are managed in other context.

*Rich Model*: 

We need their identities during runtime w/o depending on database so we can't trust primitive types to define identity so `Guid` should be used. (It totally lies within the DDD logic: Domain entities are created w/o using databases.)

-  Since it has to satisfy an invariant, their properties should not be exposed with public setters and mutation should be done through the methods.
- Therefore, the instance can be generated using the ctors with appropriate arguments and required checks must be done within the ctor to satisfy the invariant and a valid state for the object.
- Since they are mutable and has to satisfy an invariant, mutating the state/properties should be achieved through methods. Also, some internal actions might have to be taken.

```csharp
public class Event : BaseEntity<Guid>
{
    public System.Guid Id { get; }
    // For this particular type of entity (which is event) we only need some other high level info

    public int ClientId { get; private set; } // read only
    public int PatientId { get; private set; }

    public void UpdateRoom(int roomId)
    {
        // Test the roomID

        //make the changes

        // Create or raise an event
    }

    public void Schedule()
    {
        // Verify the appointment fits in schedule
        // Store the appointment
        // Raise AppointmentScheduled domain event
    }

}
```
*Misc:*
- Usually the Anemic Domain Models are generated/updated/deleted through another CRUD specific bounded context and if any changes required to these models inside another bounded context, a sync is required between these different bounded contexts.(see Eventual Consistency) These type of entities are called as `Reference Entities`.

- Entities do not support dependency injection through ctor. Only allowed through methods.


### Value Objects
These are the objects defined by its value. It exposes the following features:
- Measures, quantifies or describes a thing in the domain.
- Identity is based on composition of their property values
- Immutable
- Acts as a whole
- More precise and accurate than primitive types
- Compare using all values
- May have methods/behaviors but they should cause NO side effects. It should only compute things rather than changing the state of the objects. They are immutable.
- If a change for the state is required, create a new instance.


#### Implementation details
- Since they hold an invariant (such as entity) and immutable, their state is defined during initialization. Required checks must be done within ctor to satisfy the invariant.
- The exposed properties should be read-only. No public setters.
- No methods to change the state.
- Methods only allowed to compute derived values.
- Provide Equals, GetHashCode and comparison/equality operator overloads based on their values.

```csharp
// Base class
public abstract class ValueObject
{
    // Provides base Equality and HashCode
    // check Vladimir Kohorkov
}
```

#### Example use cases
- Usually treated as a property of an entity.
- `string`, `date`, `money`, `temperature` classes are typical examples.

```csharp
public class DateTimeRange : ValueObject
{
    public DateTime Start   { get; private set; }
    public DateTime End     { get; private set; }

    public DateTimeRange(DateTime start, DateTime end)
    {
        // Required guards and checks
        // Populate the fields
    }

    public DateTimeRange(DateTime start, TimeSpan duration)
        : this(start, start.Add(duration))
    {
        
    }

    // Convenience methods
}
```
- In C# context string is a reference type but in DDD `string` class is our favorite value object type with some methods (Replace(), ToUpper(), ToLower()) that supports immutability
- The ids can be wrapped into value objects so you can provide type safety.

```csharp
public class ClientId
{
    public readonly Guid Value;
    public ClientId()           { Value = Guid.NewGuid();}
    public ClientId(Guid id)    { Value = id; }
    // Equality and hash override
}
```

> Exception: They might not be IMMUTABLE so we should provide setters through methods like C++. Additional logic might be added here.


### Guidelines / Best practices
- Start modeling objects as Value Object. If not fit, it is entity.
- Entities can be modeled as one of the following logics:
    - Regular: has its own logic with methods. 
    - Orchestrator/container of value objects.
- Entities can provide higher level API/abstraction to be used in UseCase style of architecture.
- If an entity exposes too many primitive typed properties which act/are meaningful together, try to group them into value objects.

```csharp
public class AnimalType : ValueObject
{
    public string Species   { get; private set; }
    public string Breed     { get; private set; }

    public AnimalType(string species, string breed)
    {

    }
}
```

- Keep domain and persistence models seperate so you need an adapter/mapper between these models to communicate.
## Domain Services
If you can't find an entity or value object to place some behaviour or logic, you can define stateless services within the domain called `services`. It can be considered as free functions in C++.

- Acts as an interface between the domain model objects within the same bounded context. (Controls cross-aggregate behavior)
- Usually stateless but might have side effects on the objects it operates on.
- Create when you need it. Try not to use the previously created service.
- They can be used for orchestrating the specific workflows.
- Do not overuse. It might violate the DDD main principle.
- Proxies to external services
- May consume services from the external layers i.e. infrastructure.

## Aggregates
Provides association between entities. They are used to handle business complex logic.
- Transactional graph of entities/value objects.
- Consists of one or more entities that act as a unit against queries and commands(read & writes). Transactional consistency.(Cascade update/delete)
- Therefore, handle them as a unit for data changes(single transaction) and seek the consistency between them.
- Controls/Handles association/dependencies between entities.
- Handles complex business logic.
- Data changes to an aggregate should be A(tommic), C(onsistent), I(solated) and D(urable).
- One repository per aggregate.
- Interaction with other aggregates defines the business logic.

### Aggregate Root
Every aggregate must have a root which is the Parent object of the all members of the aggregate. It is quite possible an aggregate consists of one entity which is also an aggregate root.
- Responsible to maintain its invariants. It should always be true.
- Accessing to non-root entities only possible through aggregate root. Acts as a mediator.

### How to choose aggregate root?
Ask these questions to determine the aggregate root:
- If deleting/updating an entity (aggregate) causes cascade delete/update, then it is aggregate root.
- Does it make sense to have an object deattached from its parent? If no, it is a child aggregate, not a root.

### How to achieve associations between child aggregates and aggregate root?
- Always start with unidirectional relationships. (i.e. do not consider having navigation property to the parent entity from the child entity. You might not need that.)
- Bi-directional relationship is usually not required. It also bring circular dependency and serialization error during database operation.
    - Special attention has to be given if you have to use bi-directional relationship. Make sure your db ops are handled correctly.
    - Consider bidirectional relationship only when both end can not live w/o each other.

- ADVICE: The relationship is defined from the root to its childs in the aggregate context (One to one or many).
- Refer root aggregate from non-root aggregate with navigation property using root's id not the root aggregate itself.
- If non-root aggregates are managed from another bounded context, it is better to refer them from root aggregate using ids.

#### Cross-aggregate invariants
Creation/usage/editing one aggregate might affect another aggregate i.e. overlapping appointments. Any aggregate should not know anything about other aggregate and cross-aggregate invariants should NOT be enforced by any one aggregate so how should we solve this problem?

- Consider domain service
- Shift the logic to an upper layer by defining a new aggregate i.e. Schedule. Now, Appointment aggregate is now the child aggregate of the Schedule aggregate.

#### Implementation details
- External objects should only interact with the aggregate root not with the childs/internals.
- The invariant is satisfied within the ctor.
- Refer non-root aggregates using their ids as Foreign Key (Navigation property). (ALERT: Too many FKs to non-root entities may suggest a problem.)
    - This avoids bi-directional relationships
    - Avoids ORM confusion
```csharp
/*
Client [root] (Name and Patients)
Patient (Non - Root) holds clientId
*/
```
- Define aggregate root as base abstract class or interface.


## Domain Models vs Domain Services. Which one to choose?
Sometimes same business logic can be achieved with both using domain models(aggregates) and services. However, always try to achieve this using aggregates.
- If that does not work, use domain services.
- Example: Booking a room/appointment can be achieved with both domain aggregates and domain services.
## Domain Events
- To capture an occurence of something that happened in the domain
- Represent the past
- Typically immutable

1. Create interface or base types for Handlers & Domain event
```csharp
// 1. Custom
public interface IDomainEvent
{

}
// Option 2: Use .net event mechanism
```
2. Provide a specific implementation for an event
3. Create Handlers for each event
4. Register the events on the associated entity
5. Raise them when required.

### Event handling
Now, the question is who is handling this event? Who is(are) the listener(s)? It usually depends on the business logic but these questions typically has two answers.

- Listener in the same bounded context (process): Raising and handling the event occur in the same memory space. This usually happens when the listener is another aggregate or domain service.

- Listener in different bounded context: If this is the case, a communication contract is required between the bounded contexts. This can be achieved with several methods:
    - Http
    - Grpc
    - Message Queue
    - Sockets
    - Publisher/Subscriber

and `Service Bus` is responsible for to deliver the raised information to the listener in text (xml or json) or binary format depending on your contract.
## References



