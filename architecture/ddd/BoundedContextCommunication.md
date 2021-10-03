# Communication between/within Bounded Contexts


The communication between different apps can be achieved in various ways.
- Http
- SOaP
- Grpc
- Message Queue
- Sockets
- Publisher/Subscriber

## Anti-Corruption Layer
- Translate between foreign systems (bounded context)' models and your own
- Simplifies communication between systems
- MAy employ design patterns such as facade or adapter

[Lost in bounded context tranlations with Julie, Idnu, Micheal and Nick](youtu.be/u-5sKvh48-g)

## Message Queue
One application drops the message and the other is receiving it. The publisher does not even care about if any other app is listening or not.

The whole messaging system is controlled by a `Message Queue Server` and its most critical part is `Service Bus`:
- Makes sure that messages are delivered to the different apps which care about that message.
- Holds the info of:
    - List of known saga types
    - List of running saga instances
    - List of known handler types
- Might write the log of events occurred to the event store.

Popular Service Bus frameworks:
- [NServiceBus](particular.net/nservicebus)
- [Rebus](github.com/rebus-org/Rebus)
- [MassTransit](masstransit-project.com)

### Types of message handlers
#### Saga:
Theya are defined as stateful (optional), typically long-running instances of process.

Main features:
- Should be identified uniquely
- Might be persistent and stateful
- Might be stateless

The implementation consists of:
- Command or event that starts the associated business process
- List of commands that saga can handle
- List of events the saga is interested in.

```csharp
public class CheckoutSaga : Saga<CheckoutSagaData>,
                            IStartWith<StartCheckoutCommand>,
                            ICanHandle<CancelCheckoutCommand>,
                            ICanHandle<PaymentCompletedEvent>
{
    public void Handle(StartCheckoutCommand message)
    {

    }

    // other implss
}
```

#### Handlers 
Typically stateless, just consider basic event handlers as functions.

## Eventual consistency
When two or more syteems or bounded contexts do not need to be synced immediately but the changes will eventually get to their destination.

Ways :
- Publisher/subscriber

## Microservices
Bounded Contexts, Microservices and Everything in bewtween - Vladik Khononov