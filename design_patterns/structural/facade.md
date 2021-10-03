# Facade
In contrast to mediator, Facade provides interface (abstraction) between upper&higher layers or dependencies. If a client(layer) wants to communicate with sub layers, usgin Facade as a single interface to minimize dependencies and communication. It provides single interface and clients communicate with Facade layer rather than sub layers.

As opposed to Mediator, sub-layers would not know anything about the clients and the Facade. Clients can not access directly to the sub-layers. They are usually implemented as Singleton.

## References
- Design Patterns - Elements of Reusable Object-Oriented Software, Gamma et. al., 1994