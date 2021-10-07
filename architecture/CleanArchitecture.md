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
## Open Source Projects
- [https://github.com/ardalis/CleanArchitecture](https://github.com/ardalis/CleanArchitecture)
- [https://github.com/matthewrenze/clean-architecture-demo](https://github.com/matthewrenze/clean-architecture-demo)

## Other References
- [Layers, Onions, Ports, Adapters: it's all the same by Mark Seemann](https://blog.ploeh.dk/2013/12/03/layers-onions-ports-adapters-its-all-the-same/)

- [Gerg Young](https://goodenoughsoftware.net)
- [Udi Dahan](http://udidahan.com)
- [Vaugh Vernon](vaughvernon.com)