In software development, systems often evolve, requiring integration between components with incompatible interfaces. This challenge is where the Adapter design pattern shines. Acting as a structural design pattern, the Adapter allows objects with incompatible interfaces to collaborate seamlessly by converting one interface into another.

This article is based on information from Refactoring.Guru, and it includes examples and concepts presented on that site.

### What Is the Adapter Pattern?

Also known as the Wrapper pattern, the Adapter pattern creates a bridge between two incompatible interfaces. It enables existing code to work with new components without modification, preserving the existing functionality while integrating new capabilities.

### Problem Scenario

Consider a stock market monitoring app that fetches stock data in XML format and displays it in charts and diagrams. Now, suppose you want to integrate a powerful third-party analytics library that only works with JSON. Modifying the analytics library to support XML is impractical, especially if you lack access to its source code.

Without a solution, you face a compatibility deadlock. Enter the Adapter pattern.

### How the Adapter Pattern Works

The Adapter pattern introduces a special object, the adapter, to handle the conversion between incompatible interfaces. Here's how it operates:

1. **Client Interface:** The existing system expects data in a specific format (e.g., JSON).
2. **Service Interface:** The third-party or legacy system provides data in an incompatible format (e.g., XML).
3. **Adapter:** The adapter implements the client interface while wrapping the service object, converting incoming data to the required format.
4. **Interaction:** The client interacts with the adapter, unaware of the conversion complexity.

This separation ensures that changes in the service interface don't ripple through the client code.

### Real-World Analogy

Imagine traveling from the U.S. to Europe and trying to charge your laptop. The power outlets in Europe differ from those in the U.S. A power plug adapter bridges this gap by converting the U.S. plug to fit European sockets. Similarly, in software, the Adapter pattern converts one interface to work seamlessly with another.

### Types of Adapters

1. **Object Adapter:** Utilizes object composition by wrapping the service object within the adapter class. This is more flexible since it can work with multiple service objects.

2. **Class Adapter:** Uses inheritance to combine the interfaces of both the client and service classes. This approach is limited to languages that support multiple inheritance, such as C++.

### Pseudocode Example

To illustrate, consider adapting square pegs to fit round holes:

```java
class RoundHole {
    private double radius;

    public RoundHole(double radius) { this.radius = radius; }

    public double getRadius() { return radius; }

    public boolean fits(RoundPeg peg) {
        return this.radius >= peg.getRadius();
    }
}

class SquarePeg {
    private double width;

    public SquarePeg(double width) { this.width = width; }

    public double getWidth() { return width; }
}

class SquarePegAdapter extends RoundPeg {
    private SquarePeg peg;

    public SquarePegAdapter(SquarePeg peg) { this.peg = peg; }

    @Override
    public double getRadius() {
        return (peg.getWidth() * Math.sqrt(2)) / 2;
    }
}
```

In this example, the adapter makes the square peg behave like a round peg by calculating a compatible radius.

### When to Use the Adapter Pattern

- When you need to integrate a legacy system or third-party library with incompatible interfaces.
- When extending every subclass to add missing functionality is inefficient.
- When maintaining flexibility and scalability is crucial.

### Pros and Cons

**✅ Pros:**
✔ Adheres to the Single Responsibility Principle by separating conversion logic.
✔ Promotes the Open/Closed Principle, allowing new adapters without breaking existing code.
✔ Enables code reuse and scalability.

**❌ Cons:**
✘ Increases overall system complexity.
✘ May introduce performance overhead due to extra layers of abstraction.

### Adapter vs. Other Patterns

- **Facade:** Defines a new interface, while the Adapter makes an existing interface usable.
- **Decorator:** Extends functionality while keeping the interface unchanged.
- **Proxy:** Provides a surrogate for another object without altering the interface.

### Conclusion

The Adapter design pattern is essential for integrating incompatible systems without extensive modifications. By introducing a translation layer, it preserves existing functionality while enabling seamless collaboration with new or legacy components. Whether dealing with legacy systems, third-party libraries, or simply different data formats, the Adapter pattern offers a clean and scalable solution.
