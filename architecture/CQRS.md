# Command Query Responsibility Segregation
It is all about seperating the write and read logic from each other in the application domain.

## Task-based API
Each action that can be executed by the externals or clients can be considered as Task. Allocating separate API for each task ???

## Command
- Alter state
- Does not return data/ info
- Commands trigger reaction in the core domain
- Command handlers resides in the application layer
- Serializable method calls
- Command handlers act similarly with ASP.NET controllers.

## Query
- Does not alter state
- Returns data

## Events
- Events are the outcome of the reaction triggered by the command
- Used to external apps/listeners to inform

## dtos
- data contracts

## CQRS from the persistence viewpoint
The databases can also be splitted into command and query databases. In a typical application, number of reads/queries are more than the write operations. In order to provide better scalability and optimization, separating the databases might be a good approach.

The command database can be the master and some external mechanism keep the databases in sync.


## Best Practices / Guidelines
- Place the definition of commands/queries and their handlers in the same file.
- MAke handlers nested within the corresponding command class and mark them as internal.
- Do NOT reuse command handlers.
- Commands should not access/call other commands

## Misc.
- DDD is only required for Commands not for Query.
- Query model can be considered as a thin wrapper on top of the database. Can use database-specific features to optimize the performance ????
- ORM with enabled lazy loading leads to N+1. Use specification pattern to avoid this. Don't use ORM for the Query stack.

## Design Approaches
### Dino Esposito
- Domain layer is only designed for Command side. No Query logic lies within the domain logic.
- Simple DTOs and  direct data access for the Query stack.

#### Post-Redirect-Get
When a command operation suceeds, redirect the flow to the corresponding read action to inform the user.

#### CQRS Premium
Command and Query stacks has their own databases but they somehow must be synced. The read db should know about the changes or actual state of the command storage.
- Synchronous (Automatically up-to-date): Every command trigerrs sync udpdates.
- Async (Eventually up-to-date): Every command trigerrs async updates
- Scheduled Synch: An external job runs periodically and update the read stroge.
- On-Demand: This is lazy version. The sync is achieved when a query is requested.

#### CQRS Deluxe
Message based orhectestration

## Additional tools/patterns for CQRS Applications
### Decorator pattern
This pattern modifies the behavior of a class w/o changing its existing API without requiring to change the API's implementation. It is used to model cross-cutting concerns in the infrastructure level.

It is on top of the command/query handlers to modify their default behavior.

- ASP.NET Middleware is implemented in a similar way. Use ASP.NET middleware for ASP.NET related concerns
- However, custom decorators provide the following benefits:
    - Additional control
    - Separation of control
    - Easy to apply selectively.
    - Chain multiple decorators using reflection


## Benefits of CQRS
- Distinct optimization
    - Query stack can benefit from caching.
    - Command stack can benefit from queues.
- Scalibility potential