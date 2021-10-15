# Clean Architecture
## Layers
- Domain: Enterprise wide-logic and types
  - Interfaces
  - Entities, Value Objects and Aggregate
  - Enumerations
  - Logic
  - Exceptions
  - Services
  - Specifications(for Query operations)

- Application: Application specific business logic and types
  - Interfaces to be implemented by outer layers (i.e. Infrastructure, Persistence)
  - Models or (View Models) - Requires mapping
  - Commands / Queries
  - Validation
  - Exception

- Infrastructure: All external concerns (things not in your memory space or third parties). No layer depend on this layer.
  - Depends on Application layer only
  - The following concerns are implemented in this layer:
    - Persistence
    - Logging & Tracing
    - Notification
    - Caching
    - Identity
    - Security
    - Inversion of control
    - Networking
    - File Access
    - API Clients
    - System Clock

- Presentation Layer:
  - UI
  - Web API
    - Controllers
    - Models
  - Web App: Imho, this should be dependent on the Web API. It should not be bundled with the Web API.

- Shared Kernel (Common): Common types shared by multiple different domains/applications/bounded contexts throughout the enterprise.
  - Base Entity, Base Domain Event ,Base Specification
  - Common Exceptions and Interfaces
  - Common Auth (i.e. User Class), Common DI (i.e. extension methods), Common logging and common guard clauses.

## Various styles of clean architecture
The Clean Architecture is defined in different forms by various experts. All of them have only one purpose to do so: `Separation of Concerns`. 

The most common and known ones are as follow:
### Traditional (Three-Tier) Architecture
<p align="center">
  <img width="600" height="395" src="../images/architectures/JeffreyPalermoTraditionalArch.png">
</p>

### [Hexagonal Architecture by Alistair Cockburn](https://alistair.cockburn.us/Hexagonal+architecture)

<p align="center">
  <img width="934" height="631" src="../images/architectures/AlistairCockburnHexaArch.png">
</p>

<p align="center">
  <img width="1055" height="669" src="../images/architectures/SteveSmithHexagonalArch.png">
</p>

Hexagonal architecture is also known known as `Ports/Adapters`. Check this [link](https://www.dossier-andreas.net/software_architecture/ports_and_adapters.html) for details. 

### [Onion Architecture by Jeffrey Palermo](https://jeffreypalermo.com/2008/07/the-onion-architecture-part-1/)

<p align="center">
  <img width="669" height="506" src="../images/architectures/JeffreyPalermoOnionArch.png">
</p>

<p align="center">
  <img width="684" height="464" src="../images/architectures/JeffreyPalermoOnionArchFlattened.png">
</p>

<p align="center">
  <img width="740" height="691" src="../images/architectures/SteveSmithOnionArch.png">
</p>


### [Clean Architecture by Uncle Bob](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
<p align="center">
  <img width="661" height="503" src="../images/architectures/UncleBobCleanArch.png">
</p>

### [Clean Architecture by Steve Smith](https://youtu.be/joNTQy-KXiU?t=3545)
<p align="center">
  <img width="1161" height="580" src="../images/architectures/SteveSmithCleanArch.png">
</p>

## Guidelines - Best Practices
- Consider using OwnedEntity feature of EF Core when a value object is wrapped/part of an owner entity.

### Steve Smith
- He prefers representing features within aggregates with the help of domain events. Defines dedicated features folder in the ui layer.

- Infrastructure layer:
  - Fire events after you are done with db related operations. Save/update/delete the state first and then fire domain events.
- Web API:
  - Controller Action (or page Handlers for UI):
    1. Accept task-specific types(ViewModel, ApiModel, BindingModel)
    2. Perform and handle model validation (ideally w/filters)
    3. <Do Work>
    4. Create any model type required for the response
    5. Return an appropriate Result Type (View, Page, OK, NotFound etc.)
  - What is `Do Work`? You have different ways to handle:
    1. Repositories and Entities (This works well with the CRUD type models and simple operations)
        - Get Entity from the injected repo
        - Work on that and its methods
        - Map it accordingly
        - Update the entity's state using the repo.
    2. Work with an `application service`.
        - Pass ApiModel types to the service
        - Application service internally works with the repositories and domain model types
        - Service returns a web model type.
        - Application service is responsible for mapping between models.
        - Keep controllers ligthweight and with fewer injected dependencies.
    3. Use CQRS like approach with tools like `MediatR`.
        - Use ApiModel types that represent commands. (Use Case)
        - Send model-bound instance of command to handler using `_mediator.Send();`.
        - No need to inject seperate services to different controllers.
        - You can resolve commands using `Model Binding`.
        - Try to place `command handlers` as close to Core as you can.
  - Define `ApiEndpoint` for each action to break up the controller in a more concise way.
- Web(Presentation):
  - In order Web to wire things up, it need Infrastructure layer. This can be achieved in two ways:
    1. Referencing the Infrastructure from Web(or Presentation) layer. (Not really wanted???)
    2. Use reflection. (Make sure Infrastructure assemblies go to the output bin and Web can consume them. Dependency Injection in ASP.NET Core supports Reflection.)
- Shared Kernel (Common):
  - Prefer distributing this layer through packaging. 

### Jason Taylor
- Domain Layer: 
  - Avoid using data annotations
  - Use Value objects where appropriate
  - Initialize all collections and use private setters.
  - Consider using [AuditableEntity](https://youtu.be/dK4Yb6-LxAk?t=885)
- Application Layer:
  - [Using CQRS and MediatR together brings great power](https://youtu.be/dK4Yb6-LxAk?t=1027)
  - Use `AutoMapper` for mapping
  - Provide an extension method for dependency injection to be consumed by upper layer using `Microsoft.Extensions.DependencyInjection`.
  - MEdiatR simplifies cross cutting concerns.(?)
- Infrastructure Layer:
  - EF Core configurations are handled in this layer.[Youtube](https://youtu.be/dK4Yb6-LxAk?t=2165)
  - Provide an extension implementation to be used by dependency injection(https://youtu.be/dK4Yb6-LxAk?t=2373).

## Useful APIs
- [MediatR](https://github.com/jbogard/MediatR)
- [AutoMapper](https://github.com/AutoMapper/AutoMapper)
- [FluentValidation](https://github.com/FluentValidation/FluentValidation)
- [Ardalis.GuardClauses](https://github.com/ardalis/GuardClauses)
- [Ardalis.ApiEndpoints](https://github.com/ardalis/ApiEndpoints)
- [CsvHelper](https://github.com/JoshClose/CsvHelper)
- [NSwag](https://github.com/RicoSuter/NSwag)
- [PapercutStmp - Email server](https://github.com/ChangemakerStudios/Papercut-SMTP)
  - [Docker containerized PapercutStmp](https://hub.docker.com/r/jijiechen/papercut)
### Testing
- [Shouldly](https://github.com/shouldly/shouldly)
- [FluentAssertions](https://github.com/fluentassertions/fluentassertions)
  - [MsTest Migration Tips](https://fluentassertions.com/tips/#mstest-migration)

## Open Source Projects
- [Clean Architecture template by Steve Smith](https://github.com/ardalis/CleanArchitecture)
- [Clean Architecture Demo for PluralSight by Matthew Renze](https://github.com/matthewrenze/clean-architecture-demo)
- [Clean Architecture Solution Template by Jason Taylor](https://github.com/jasontaylordev/CleanArchitecture)
- [Northwind Traders Sample Project by Jason Taylor](https://github.com/jasontaylordev/NorthwindTraders)
- [Microsoft - eShop on the web - Simple](https://github.com/dotnet-architecture/eShopOnWeb)
- [Microsoft - eShop on Containers w/ Microservices - Advanced](https://github.com/dotnet-architecture/eShopOnContainers)
- [Clean Architecture with .NET Core & React+Redux](https://github.com/ivanpaulovich/clean-architecture-manga)
- [ASP.NET Core WebApi - Clean Architecture](https://github.com/iammukeshm/CleanArchitecture.WebApi)
- [Clean Architecture Template BY IAYTI](https://github.com/iayti/CleanArchitecture)
- [BlazorHero](https://github.com/blazorhero/CleanArchitecture)

## Other References
- [Layers, Onions, Ports, Adapters: it's all the same by Mark Seemann](https://blog.ploeh.dk/2013/12/03/layers-onions-ports-adapters-its-all-the-same/)

- [Gerg Young](https://goodenoughsoftware.net)
- [Udi Dahan](http://udidahan.com)
- [Vaugh Vernon](vaughvernon.com)

## Videos
### Jason Taylor
- [Clean Architecture with ASP.NET Core 3.0 - Jason Taylor - NDC Sydney 2019](https://www.youtube.com/watch?v=5OtUm1BLmG0)
- [Clean Architecture with ASP.NET Core 3.0 • Jason Taylor • GOTO 2019](https://www.youtube.com/watch?v=dK4Yb6-LxAk)
- [Clean Testing: Clean Architecture with .NET Core - Jason Taylor - NDC Sydney 2020](https://www.youtube.com/watch?v=hV43fiHYBb4)
### Steve 'Ardalis' Smith
- [Visual Studio Toolbox Live - Clean Architecture for ASP.NET Core Apps](https://www.youtube.com/watch?v=GYZcds1FjEM)
- [Clean Architecture with ASP.NET Core with Steve "Ardalis" Smith (2020-06-01)](https://www.youtube.com/watch?v=joNTQy-KXiU)
- [Clean Architecture with ASP.NET Core by Steve Smith | BRDNUG June 2021](https://www.youtube.com/watch?v=D6IEnMTuawA)

### Jimmy Bogard
- [Vertical Slice Architecture](https://www.youtube.com/watch?v=SUiWfhAhgQw)
### Ian Cooper
- [Implementing the Clean Architecture in .NET Core - Ian Cooper](https://www.youtube.com/watch?v=IAcxetnsiCQ)
- [On .NET Live - Commands, Queries, and Clean Architecture](https://www.youtube.com/watch?v=IRvDGPbxdTs)

### Gill Cleeren
- [Gill CLEEREN: Clean architecture with ASP.NET Core | UCP2019](https://www.youtube.com/watch?v=BxtHt7tsX-c)
### Misc
- [Clean Architecture - .NET Core 5 Web API Microservices - Fundamentals](https://www.youtube.com/watch?v=TEeb0Hba8jI)
- [Microservice Series - From Zero to Hero by Wolfgang Ofner](https://www.programmingwithwolfgang.com/microservice-series-from-zero-to-hero)