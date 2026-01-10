# Factory and Abstract Factory Pattern

Creational patterns that deal with object creation mechanisms, trying to create objects in a manner suitable to the situation.

## Real-Time Use Case: Cross-Platform UI Kits
Imagine building a UI library that needs to work on **Windows and Mac**.
- On Windows, you need `WindowsButton` and `WindowsCheckbox`.
- On Mac, you need `MacButton` and `MacCheckbox`.
An **Abstract Factory** allows the client to say "Give me a UI Factory for Mac", and then request buttons/checkboxes without knowing the concrete classes.

## Advanced Concepts
- **Factory Method**: Defines an interface for creating an object, but lets subclasses decide which class to instantiate.
- **Abstract Factory**: Provides an interface for creating families of related or dependent objects without specifying their concrete classes.

## Example Code (C#)

```csharp
// Products
public interface IButton { void Render(); }
public class WindowsButton : IButton { public void Render() => Console.WriteLine("Rendering Windows Button"); }
public class MacButton : IButton { public void Render() => Console.WriteLine("Rendering Mac Button"); }

// Abstract Factory
public interface IUIFactory {
    IButton CreateButton();
}

// Concrete Factories
public class WindowsFactory : IUIFactory {
    public IButton CreateButton() => new WindowsButton();
}

public class MacFactory : IUIFactory {
    public IButton CreateButton() => new MacButton();
}

// Client
var factory = new WindowsFactory(); // Or MacFactory at runtime
var button = factory.CreateButton();
button.Render();
```

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Loose Coupling**: Client doesn't depend on concrete implementation. | **Complexity**: Code can become complicated due to many new interfaces and classes. |
| **Single Responsibility**: Creation code is in one place. | **Maintenance**: If you add a new product (e.g., `CreateSlider`), you must update all factories. |
| **Open/Closed Principle**: You can introduce new variants without breaking client code. | |

## When to use?
Use when your code needs to work with various families of related products, but you don't want it to depend on the concrete classes of those products—which might be unknown beforehand or you simply want to allow for future extensibility.
