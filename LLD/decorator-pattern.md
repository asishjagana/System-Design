# Decorator Pattern

The Decorator pattern allows behavior to be added to an individual object, dynamically, without affecting the behavior of other objects from the same class.

## Real-Time Use Case: Coffee Shop POS or Stream IO
Think of a **Starbucks** order. You start with a `PlainCoffee`. Then you can add `Milk`, `Sugar`, `Mocha`, `Caramel`.
Instead of creating dozens of classes like `CoffeeWithMilkAndSugar`, you "decorate" the base coffee object with additive behaviors.

## Example Code (C#)

```csharp
public interface ICoffee { double GetCost(); }

public class PlainCoffee : ICoffee {
    public double GetCost() => 2.00;
}

// Decorator Base
public abstract class CoffeeDecorator : ICoffee {
    protected ICoffee _coffee;
    public CoffeeDecorator(ICoffee coffee) => _coffee = coffee;
    public virtual double GetCost() => _coffee.GetCost();
}

// Concrete Decorators
public class MilkDecorator : CoffeeDecorator {
    public MilkDecorator(ICoffee coffee) : base(coffee) { }
    public override double GetCost() => base.GetCost() + 0.50;
}

public class SugarDecorator : CoffeeDecorator {
    public SugarDecorator(ICoffee coffee) : base(coffee) { }
    public override double GetCost() => base.GetCost() + 0.20;
}

// Usage
ICoffee myOrder = new PlainCoffee();
myOrder = new MilkDecorator(myOrder);
myOrder = new SugarDecorator(myOrder);
Console.WriteLine($"Total: ${myOrder.GetCost()}"); // Output: $2.70
```

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Flexibility**: Combine behaviors at runtime (Infinite combinations). | **Nested Complexity**: Deeply nested decorators can be hard to debug (a decorator of a decorator of a decorator). |
| **Follows SRP**: Each decorator class handles exactly one piece of logic. | **Small Classes**: Can result in many small classes in your codebase. |
| **Alternative to Inheritance**: Prevents "Class Explosion" (e.g., avoiding 500 subclasses for every combo). | |

## When to use?
Use when you want to assign extra behaviors to objects at runtime without breaking the code that uses these objects. Common in **IO Streams** (e.g., `BufferedStream(FileStream(...))`).
