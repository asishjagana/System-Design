# Design Patterns - Strategy Pattern

The Strategy Pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable. It lets the algorithm vary independently from clients that use it.

## Real-Time Use Case
**E-commerce Payment Gateway**: A checkout system needs to support multiple payment methods like **Credit Card, PayPal, and Bitcoin**. Instead of using a giant `switch` statement, you can use the Strategy Pattern.

## Example Code (C#)

```csharp
// Strategy Interface
public interface IPaymentStrategy {
    void Pay(double amount);
}

// Concrete Strategies
public class CreditCardPayment : IPaymentStrategy {
    public void Pay(double amount) => Console.WriteLine($"Paid {amount} using Credit Card.");
}

public class PaypalPayment : IPaymentStrategy {
    public void Pay(double amount) => Console.WriteLine($"Paid {amount} using PayPal.");
}

// Context
public class ShoppingCart {
    private IPaymentStrategy _paymentStrategy;

    public void SetPaymentStrategy(IPaymentStrategy strategy) {
        _paymentStrategy = strategy;
    }

    public void Checkout(double amount) {
        _paymentStrategy.Pay(amount);
    }
}

// Usage
var cart = new ShoppingCart();
cart.SetPaymentStrategy(new PaypalPayment());
cart.Checkout(100.00); 
```

## Key Benefits
- **Flexibility**: Switch algorithms at runtime.
- **Clean Code**: Removes complex conditional logic.
- **Maintainable**: Adding a new strategy doesn't affect existing ones.
