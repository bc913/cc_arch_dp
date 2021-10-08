# Dependency Inversion
The direction of the dependency should point towards the absraction not implementation.
Features:
- Testable
- Maintainable
- Modular

## Explicit Dependencies
- Be explicit about dependencies by injecting them through constructors.
- Provides clearer contract
- If a dependency is not required throught the whole lifetime of the consumer, then pass it as an argument to consumer's public methods.

## References
- [Visual Studio Toolbox Live - Clean Architecture for ASP.NET Core Apps](https://www.youtube.com/watch?v=GYZcds1FjEM)
- [Gill CLEEREN: Clean architecture with ASP.NET Core | UCP2019](https://www.youtube.com/watch?v=BxtHt7tsX-c)
