# Parking Lot - Low Level Design

Designing a Parking Lot is a classic LLD interview question. It tests your ability to model real-world entities into classes and interfaces.

## Requirements
1. Multiple floors.
2. Multiple types of spots (Small, Compact, Large).
3. Different vehicle types (Motorcycle, Car, Bus).
4. Entry and Exit points with automated ticketing.

## Class Diagram Concepts
- `Vehicle` (Abstract) -> `Car`, `Motorcycle`, `Bus`
- `ParkingSpot` -> `CompactSpot`, `LargeSpot`
- `Level` (Represents a floor)
- `ParkingLot` (Singleton)
- `Ticket`

## Example Implementation Snippet (C#)

```csharp
public enum VehicleType { Car, Motorcycle, Bus }

public abstract class Vehicle {
    public string LicensePlate { get; set; }
    public VehicleType Type { get; set; }
}

public class ParkingSpot {
    public int Id { get; set; }
    public bool IsFree { get; private set; } = true;
    public Vehicle OccupyingVehicle { get; private set; }

    public void AssignVehicle(Vehicle v) {
        OccupyingVehicle = v;
        IsFree = false;
    }

    public void RemoveVehicle() {
        OccupyingVehicle = null;
        IsFree = true;
    }
}

public class Level {
    private List<ParkingSpot> _spots;
    public int FloorNumber { get; set; }

    public bool Park(Vehicle v) {
        var spot = _spots.FirstOrDefault(s => s.IsFree);
        if (spot != null) {
            spot.AssignVehicle(v);
            return true;
        }
        return false;
    }
}
```

## Advanced Considerations
### 1. Concurrency Control
In a real system, multiple entry/exit points are checking for free spots simultaneously.
- **Optimistic Locking**: Check spot status, try to assign, and if someone else beat you to it (version mismatch), retry.
- **Pessimistic Locking**: `lock` the database row for the spot while the transaction is active.

### 2. Strategy Pattern for Pricing
Fees vary by vehicle type and time spent. Use the **Strategy Pattern** to swap `FlatRatePricing`, `HourlyPricing`, or `EarlyBirdPricing` without changing the `Ticket` logic.

## Pros and Cons of this Design
| Pros | Cons |
| :--- | :--- |
| **Modularity**: Easy to add new vehicle types or floor layouts. | **Complexity**: Handling all edge cases (lost tickets, broken gates) makes the code messy. |
| **Scalability**: Can be extended to manage multiple parking lots via a central service. | **Over-engineering**: For a small personal lot, a simple list of booleans might suffice. |
| **Thread Safety**: Clean separation of concerns allows for easier locking strategies. | |

## Key Benefits
- **Separation of Concerns**: Each class has a clear, minimal role.
- **Extensibility**: Adding a "VIP Spot" or an "Electric Charging Station" is trivial.
