# Flyweight
This main purpose of this pattern is to gain efficiency in memory usage. The logic lies on the idea of storing and using the shared data in a seperate class for widely used and has to be generated objects. The instances of shared data classes are called flyweight.

## Terminology:
- `Context-free` or `intrinsic` state: It is stored within flyweight object and consists of data which is independent of context it is being used. i.e. a `soldier` object in a game has some common properties throught the entire army so no need to generate thousands of that soldier objects. Modeling it as a `flyweight` would keep the shared data in a single instance provides a considerable amount of memorry space. The data for the `flywieght` objects is usually immmutable.

- `Context-related` or `extrinsic` state: This is the context related state info and can't be shared. Each instance most likely has its own unique data so can't be a part of the `intrinsic` state. This data can be hold more efficiently using appropriate data structures. Extrinsic state can be computed or stored. This state can also be passed to flyweight objects to compute the required data.

- `Flyweight factory` or `Flyweight-pool`: This is the pool where required `flywieght` objects are generated. Usually it is modeled as singleton. Keeps the registry of th flyweight objects. If there is no requested `flyweight` object generate one, otherwise return the existing one.

## References
- Design Patterns - Elements of Reusable Object-Oriented Software, Gamma et. al., 1994
- [https://gameprogrammingpatterns.com/flyweight.html](https://gameprogrammingpatterns.com/flyweight.html)