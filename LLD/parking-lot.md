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

## Considerations
- **Concurrency**: What if two cars try to park in the same spot at the exact same millisecond? (Use locks/semaphores).
- **Payment**: Integrating a payment service.
