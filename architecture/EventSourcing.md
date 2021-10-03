# Event Sourcing
All changes made to the application state during lifetime of the application are stored as a aequence of events.

## What is an event?
An event is something happened in the past and should be named accordingly.
## Main Features
- Requires CQRS.
- Once stored, they are immutable
- Can be duplicated or replicated
- Replaying the event does not require to repeat the behavior.

## How it works?
Works in accordance with CQRS. Commands are considered as natural stream of events. Queries works much better when you have a structured data.

## Implementation of the Evets
It should consist of: 
- Requires globally unique event ID
- Code for the operation
- Timestamp
- Entity identifier
- Full details (create) - changes applied(update)

For the Query operations, full or partial replay of stream of events is required. You can get the Events as list and replay them on the server side to generate the corresponding aggregate.
```csharp
public IEnumerable<GenericEvent> GetEventStream(string id)
{
    return DocumentSession
            .Query<GenericEvent>()
            .Where(e=>e.AggregateId == id)
            .OrderBy(e=>e.TimeStamp)
            .ToList();
}

public Aggregate PlayEvents(string id, IEnumerable<GenericEvent> events)
{
    var result = new Aggregate(id);
    foreach(var e in events)
    {
        if(e is AggregateCreatedEvent)
            result.Create(e.Data);
        else if(e is AggregateUpdatedEvent)
            result.Update(e.Data);
        else if(e is AggregateDeletedEvent)
            result.Delete(e.Data);
    }

    return result;
}
```
## Where to store events?
In order to establish a business logic with event sourcing, the events are required to be persisted. A persistent store for events.
- Append-only, no delete.
- Replay the related events starting at t0(beginning or a known point, snapshot) to get the last state of an entity.

There are some implementations for event data store. The provided APIs typically consist of:
- Writing events to a stream
- Reading events from a stream
- Subscribing to stream to get updates
    1. Volatile Subscriptions: Callback a function whenever an event is written to a given stream until unsubscribing.
    2. Catch-up Subscription: Callback a function from a given position up to the end and then turns into volatile
    3. Persistent Subscription: Multiple consumers are guaranteed to receive at least one notification of events written possibly more.

## References
- [Modern Software Architecture: Domain Models, CQRS and Event Sourcing by Dino Esposito]