# Builder Pattern

The Builder pattern separates the construction of a complex object from its representation so that the same construction process can create different representations.

## Real-Time Use Case: Custom PC Builder or Query Builders
Imagine a system to build a **Custom PC**. A PC has many optional parts: GPU, CPU, RAM, SSD, Liquid Cooling, RGB Lights.
Instead of a giant constructor like `new PC(32, 1024, "RTX4090", true, false, ...)` which is hard to read and manage, you use a Builder.

## Example Code (C#)

```csharp
public class Computer {
    public string CPU { get; set; }
    public int RAM { get; set; }
    public string GPU { get; set; }
    public bool WaterCooled { get; set; }
}

public class PCBuilder {
    private Computer _pc = new Computer();

    public PCBuilder AddCPU(string cpu) { _pc.CPU = cpu; return this; }
    public PCBuilder AddRAM(int ram) { _pc.RAM = ram; return this; }
    public PCBuilder AddGPU(string gpu) { _pc.GPU = gpu; return this; }
    public PCBuilder EnableWaterCooling() { _pc.WaterCooled = true; return this; }

    public Computer Build() => _pc;
}

// Usage
var myPC = new PCBuilder()
    .AddCPU("i9-14900K")
    .AddRAM(64)
    .AddGPU("RTX 4090")
    .EnableWaterCooling()
    .Build();
```

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Readability**: Fluent interface makes code look like a sentence. | **Verbosity**: Requires creating many new classes for each complex object. |
| **Step-by-Step Construction**: You can defer construction steps or run them recursively. | **Overkill**: Not needed for simple objects with 2-3 parameters. |
| **Immutability**: You can validate the object state before finalizing the `Build()`. | |

## When to use?
Use when an object has many optional parameters or when its construction involves a lot of steps that need to be performed in a specific order. Common in Library design (e.g., `HttpClientBuilder`).
