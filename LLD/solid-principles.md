# SOLID Principles

SOLID is an acronym for five design principles intended to make software designs more understandable, flexible, and maintainable.

## 1. Single Responsibility Principle (SRP)
A class should have one, and only one, reason to change.
- **Example**: A `Report` class should only deal with report data. A separate `ReportFormatter` should handle how it looks.

## 2. Open/Closed Principle (OCP)
Software entities should be open for extension, but closed for modification.
- **Example**: Use interfaces. If you want to add a new payment method, create a new class implementing `IPayment`, don't modify the existing `PaymentProcessor` class.

## 3. Liskov Substitution Principle (LSP)
Objects of a superclass should be replaceable with objects of its subclasses without breaking the application.

## 4. Interface Segregation Principle (ISP)
Clients should not be forced to depend upon interfaces they do not use.
- **Example**: Instead of one giant `IMachine` interface with `Print`, `Scan`, and `Fax`, create `IPrinter` and `IScanner`.

## 5. Dependency Inversion Principle (DIP)
Depend on abstractions, not concretions.
- **Example**: High-level modules should not depend on low-level modules. Both should depend on interfaces.

## Example Code (C#)

```csharp
// BAD: Violates SRP
public class Invoice {
    public void Add() { /* Add to DB */ }
    public void Print() { /* Printing Logic */ }
}

// GOOD: Follows SRP and OCP
public interface IPrinter {
    void Print(Invoice invoice);
}

public class Invoice {
    public double Amount { get; set; }
    // Logic only related to Invoice data
}

public class PdfPrinter : IPrinter {
    public void Print(Invoice invoice) {
        Console.WriteLine("Printing PDF Invoice...");
    }
}
```
