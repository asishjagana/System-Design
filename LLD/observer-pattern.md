# Design Patterns - Observer Pattern

The Observer pattern defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

## Real-Time Use Case
**Stock Market App**: Multiple UI widgets (Price Chart, Portfolio View, Alerts) all need to update immediately when a stock price changes. Instead of each widget checking the price constantly (Polling), the `StockPrice` object notifies them when a change happens.

## Example Code (C#)

```csharp
public interface IObserver {
    void Update(double price);
}

public interface ISubject {
    void Register(IObserver observer);
    void Notify();
}

public class StockPrice : ISubject {
    private List<IObserver> _observers = new List<IObserver>();
    private double _price;

    public void SetPrice(double price) {
        _price = price;
        Notify();
    }

    public void Register(IObserver observer) => _observers.Add(observer);

    public void Notify() {
        foreach (var observer in _observers) {
            observer.Update(_price);
        }
    }
}

public class MobileAppUI : IObserver {
    public void Update(double price) => Console.WriteLine($"UI Updated: Price is {price}");
}
```

## Key Benefits
- **Loosely Coupled**: Subject doesn't need to know the specifics of Observers.
- **Dynamic**: Observers can be added/removed at runtime.
