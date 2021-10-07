# Domain Driven Design

- [Domain Layer](DomainLayer.md)
    - Entities & Value Objects & Aggregates
    - Domain Events
    - Domain Services
- Application Layer
    - Exceptions
    - Services
    - Interfaces to be implemented by the Infrastructure layer
- Infrastructure Layer: Layer to fuel the system not binding the system to a specific product. Concrete details of technologies.
    - [Persistence](PersistenceLayer.md)
    - Logging & Tracing
    - Caching
    - Security
    - Inversion of control
    - Networking
    - File Access
    - API Clients
- Shared Kernel (Common)
- [Communication between/within Bounded Contexts](BoundedContextCommunication.md)

## Open source projects
- [github.com/ardalis/pluralsight-ddd-fundamentals](github.com/ardalis/pluralsight-ddd-fundamentals)
- [Console App showing domain Events by Steve Smith](https://github.com/ardalis/domaineventsconsole)


## References
- [Domain-Driven Design Fundamentals by Julie Lerman & Steve Smith]
- [Modern Software Architecture: Domain Models, CQRS, and Event Sourcing by Dino Esposito]
- [Domain Driven Design by Martin Fowler](https://martinfowler.com/bliki/DomainDrivenDesign.html)
- [Services in DDD - Jimmy Bogard](bit.ly/1ifravE)
- [domainlanguage.com](domainlanguage.com)
- vimeo.com/125769142
### Event design - storming
- [https://eventmodeling.org/](https://eventmodeling.org/)
- [https://www.eventstorming.com/](https://www.eventstorming.com/)
- [miro.com](miro.com)