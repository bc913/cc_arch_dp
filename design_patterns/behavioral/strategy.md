# Strategy (Policy) Pattern


## Elements
- Context: Has a reference to a strategy and invokes it.
- IStrategy: Defines the contract
- StrategyImpl: Specific implementation of IStrategy

## Reasoning
When a specific task or series of tasks can be implemented in different ways, it is better to use abstraction to decouple the context from the implementation. It also provides better testability and scalability. Strategy pattern is used to abstract behaviors for specific tasks.

## How to define?
```csharp
public interface ITaxCalculationStrategy
{
    decimal Calculate(Order order);
}

// Optional
public abstract class TaxCalculationBase : ITaxCalculationStrategy
{   
    // let the derived classes to implement
    public abstract decimal Calculate(Order order);

    // Provide some common functionality to the derived classes
    // ... for convenience
    protected string GenerateCalculationScheme(Order order)
    {
        // some default impl
    }
}

// Implementations
public class UsTaxCalculationStrategy : ITaxCalculationStrategy
{
    public decimal Calculate(Order order)
    {
        // US specific impl
    }
}
```
## How to consume?
```csharp
public enum TaxLocation
{
    NotDefined,
    US,
    Sweeden
}

// Option 1: Dependency injection via ctor
public class Order
{
    private ITaxCalculationStrategy _taxCalculationStrategy;
    public Order(ITaxCalculationStrategy taxCalcStrategy)
    {
        _taxCalculationStrategy = taxCalcStrategy 
        ?? throw new ArgumentNullException(nameof(taxCalcStrategy));
    }

    public decimal GetTax()
    {
        return _taxCalculationStrategy.Calculate(this);
    }
}

// Option 2: Dependency injection via method argument
public class TaxCalculator
{
    public decimal Execute(Order order, ITaxCalculationStrategy strategy)
    {
        return strategy.Calculate(order);
    }
}

// Pass the instance to the context
public void CheckOut()
{
    //....
    TaxLocation tLoc = DetermineTaxLocation();
    ITaxCalculationStrategy taxCalcStrategy = null;
    switch(tLoc)
    {
        case US:
            taxCalcStrategy = new UsTaxCalculationStrategy();
            break;
        case Sweeden:
            taxCalcStrategy = new SweedenTaxCalculationStrategy();
            break;
        default:
            throw new InvalidOperationException();
    }

    var taxCalculator = new TaxCalculator(taxCalcStrategy);
    var salesTax = taxCalculator.Execute(order, taxCalcStrategy);
    
}
```

## Use Cases
- InvoiceStrategy (File, Email, PrintOnDemand)
- ShippingStrategy (Dhl, Usps,) -> PRovide integration with shipping providers. Plugin their APIs
- IComparer can be considered as an example of IStrategy

## References
- [C# Design Patterns - Filip Ekberg]