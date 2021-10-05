# Bridge Pattern
This pattern is used to decouple the public API of a class from its implementation by storing a field/property for the interface of the implementation. This yields to the fact that the implementation is represented by an interface so the implementations can be customized based on the business needs.

## When to use?
If a class can provide multiple implementations through a single public API, this pattern is benefical to use.

## Implementations
### C#
- Define an interface to be implemented by the concrete implementations
```csharp
public interface ImplProtocol
{
    void DoSth();
}
```

- Define the implementations:  
```csharp
public class Impl_A : ImplProtocol
{
    void DoSth() {/* Do somtehing for A*/}
}

public class Impl_B : ImplProtocol
{
    void DoSth() {/* Do somtehing for B*/}
}
```

- Define the consumer class
```csharp
// Optional (depends on your business logic and existing code layout)
public interface ConsumerProtocol
{
    void DoSomething();
}

// Optional: Provide common functionality in the abstract base class
public abstract class ConsumerBase : ConsumerProtocol
{
    private readonly ImplProtocol _impl;
    public ConsumerBase(ImplProtocol impl)
    {
        _impl = impl ?? throw new ArgumentNullException(nameof(impl));
    }

    public void DoSomething()
    {
        _impl.DoSth();
    }
}

public class ConsumerA : ConsumerBase
{
    public class ConsumerA(ImplProtocol impl) : base(impl)
    {

    }
}

public class ConsumerB : ConsumerBase
{
    public class ConsumerB(ImplProtocol impl) : base(impl)
    {

    }
}

public class ConsumerC : ConsumerProtocol
{
    private readonly ImplProtocol _impl;
    public class ConsumerC(ImplProtocol impl)
    {
        _impl = impl ?? throw new ArgumentNullException(nameof(impl));
    }

    public void DoSomething()
    {
        // custom pre conditions
        impl.DoSth();
        // custom post conditions
    }    
}
```
### C++
This pattern is also known as an utilized version of `Handle/Body` pattern which is `Handle/Body Hierarchy`. This pattern can be achieved in C++ using famous `Pimpl - Pointer to Implementation` idiom. Check references for the detailed explanation of the pimpl idiom. 

## References
- [Design Patterns - Elements of Reusable Object-Oriented Software]
- [C# Design Patterns: Bridge - Vladimir Khorikov]
### Pimpl
- Pimpl resources from Herb Sutter
    - [Compilation Firewalls](http://www.gotw.ca/gotw/024.htm)
    - [Fast pimpl idiom](http://www.gotw.ca/gotw/028.htm)
    - [Pimpls - Beauty Marks You Can Depend On](http://www.gotw.ca/publications/mill04.htm)
    - [The Joy of Pimpls (or, More About the Compiler-Firewall Idiom)](http://www.gotw.ca/publications/mill05.htm)
    - [C++11 version of Compilation Firewalls](https://herbsutter.com/gotw/_100/)
    - [Compilation Firewalls - Part 2](https://herbsutter.com/gotw/_101/)
- [https://sites.google.com/a/gertrudandcope.com/info/Publications/Patterns/C--Idioms](https://sites.google.com/a/gertrudandcope.com/info/Publications/Patterns/C--Idioms)
- [How to implement the pimpl idiom by using unique_ptr - fluentcpp.com](https://www.fluentcpp.com/2017/09/22/make-pimpl-using-unique_ptr/)
- [Pimpl idiom with modern C++](https://bitboom.github.io/pimpl-idiom)
- [The Pimpl Pattern: What You Should Know](https://dzone.com/articles/the-pimpl-pattern-what-you-should-know)
- [Boost: serialization](https://www.boost.org/doc/libs/1_77_0/libs/serialization/doc/pimpl.html)