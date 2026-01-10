# Singleton Pattern (Advanced)

Ensures that a class has only one instance and provides a global point of access to it.

## Real-Time Use Case: Logging or DB Connection Pools
In a web application, you don't want to create a new **Database Connection Pool** or a **Log File Handler** for every single request. Using a Singleton ensures all parts of the app share one instance, managing resources efficiently.

## Advanced Concepts: Thread Safety
In multi-threaded environments, a simple singleton can fail if two threads check if the instance is null at the same time.

### Double-Checked Locking
This technique minimizes the overhead of acquiring a lock by first checking the instance without a lock.

## Example Code (C# - Thread Safe Singleton)

```csharp
public sealed class DatabaseConnection {
    private static DatabaseConnection _instance = null;
    private static readonly object _lock = new object();

    // Private constructor prevents instantiation from outside
    private DatabaseConnection() {
        Console.WriteLine("DB Connection Initialized.");
    }

    public static DatabaseConnection Instance {
        get {
            // First check (no lock)
            if (_instance == null) {
                lock (_lock) {
                    // Second check (with lock)
                    if (_instance == null) {
                        _instance = new DatabaseConnection();
                    }
                }
            }
            return _instance;
        }
    }
}
```

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Resource Management**: Saves memory by preventing multiple instances. | **Hard to Test**: Singletons act as global state, making unit testing difficult (Mocking is hard). |
| **Controlled Access**: Can manage exactly when and how the instance is accessed. | **Violation of SRP**: The class often handles its own lifecycle AND its business logic. |
| **Lazy Initialization**: Created only when actually needed. | **Concurrency Issues**: Requires careful implementation to be thread-safe. |

## When to use?
Use for shared resources like configurations, caching, or hardware access (e.g., a printer driver). Avoid using singletons just to have a global variable; it's often considered an anti-pattern if misused.
