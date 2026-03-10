# Chapter 1: Clean Coding

## Introduction

Code is read far more than it is written. The best code communicates its intent clearly to future readers — teammates, your future self, or anyone who needs to debug or extend the system. This chapter covers the core principles of clean code, drawing from Robert C. Martin's *Clean Code*, with practical Java examples throughout.

---

## 1. What Is Clean Code?

Clean code is easy to understand, easy to change, and does exactly what it appears to do. It is not about cleverness or brevity — it is about clarity.

Key themes covered in this chapter:

| Topic | Summary |
|---|---|
| **Meaningful Names** | Names that reveal intent |
| **Functions** | Small, focused, well-named |
| **Comments** | Self-documenting code that rarely needs them |
| **Formatting** | Consistent visual structure |
| **Classes & SRP** | One responsibility per class |
| **SOLID Principles** | Five guidelines for good OO design |
| **Code Smells** | Recognizing and fixing poor design |
| **Testing & Reviews** | Catching problems early |
| **Conventions** | Consistency across a codebase |

---

## 2. Meaningful Names

A name should tell you *why* something exists, *what* it does, and *how* it is used. If it needs a comment to explain it, the name is not good enough.

### Use Intention-Revealing Names

```java
// Bad
public double a(double x, double y) { return x * y; }

// Good
public double calculateArea(double length, double width) { return length * width; }
```

### Avoid Disinformation and Noise

Don't name a `Set` an `accountList`. Don't use near-identical names like `productInfo`, `productData`, `productRecord` — use names with distinct meanings: `productName`, `productDescription`, `productSerialNumber`.

### Use Pronounceable, Searchable Names

```java
Date genymdhms;       // Bad: impossible to say aloud
Date generationTimestamp; // Good
```

### Naming Conventions at a Glance

| Element | Convention | Example |
|---|---|---|
| Variables | `camelCase`, noun | `studentAge`, `orderTotal` |
| Methods | `camelCase`, verb | `calculateTotal()`, `isValid()` |
| Classes | `PascalCase`, noun | `PaymentProcessor` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRY_COUNT` |
| Packages | `lowercase` | `com.university.enrollment` |
| Booleans | `camelCase`, question | `isActive`, `hasPermission` |

---

## 3. Functions

### Do One Thing

A function should do *one thing*, do it *well*, and do it *only*. The classic sign of a bloated function is comments marking internal sections like `// validate`, `// calculate total`, `// send email`. Extract each section into its own method.

```java
// Bad: one method doing everything
public void processOrder(Order order) {
    // validate, calculate, save, email, update inventory...
}

// Good: each method does one thing
public void processOrder(Order order) {
    validateOrder(order);
    calculateTotal(order);
    saveOrder(order);
    sendConfirmationEmail(order);
    updateInventory(order);
}
```

### Other Function Rules

- **Use descriptive names.** `handleUserRegistrationRequest()` beats `handle()`.
- **Minimize parameters.** Zero is ideal; three or more is a warning sign. Group related parameters into an object.
- **Avoid side effects.** A function named `checkPassword` should only check a password — not initialize a session or log a user in.

---

## 4. Comments

> *The best comment is the one you found a way not to write.*

Instead of explaining *what* code does with a comment, refactor the code so it explains itself.

```java
// Bad: comment compensates for unclear code
if ((employee.flags & HOURLY_FLAG) != 0 && employee.age > 65) { ... }

// Good: code is self-explanatory
if (employee.isEligibleForFullBenefits()) { ... }
```

### When Comments Are Appropriate

- Legal / license headers
- Explaining *why* a decision was made (not what the code does)
- Javadoc for public APIs
- TODO markers for future work

### Comments to Avoid

- Redundant comments (`int count = 0; // set count to zero`)
- Commented-out code — delete it; Git remembers
- Journal entries (`// Fixed by John, 2024-01-15`) — use Git history instead

---

## 5. Formatting

Consistent formatting is not cosmetic — it directly affects readability.

- **Vertically:** declare variables near where they are used; use blank lines as visual paragraph breaks; put high-level functions before their helpers (newspaper structure).
- **Horizontally:** 4 spaces per indent level; lines under 120 characters; spaces around operators.

```java
// Bad
public class OrderProcessor{
private Database db;
public void process(Order o){double t=0;for(OrderItem i:o.getItems()){t+=i.getPrice()*i.getQuantity();}o.setTotal(t);db.save(o);}}

// Good
public class OrderProcessor {

    private Database db;

    public void process(Order order) {
        double total = 0;
        for (OrderItem item : order.getItems()) {
            total += item.getPrice() * item.getQuantity();
        }
        order.setTotal(total);
        db.save(order);
    }
}
```

---

## 6. Classes and the Single Responsibility Principle

**A class should have only one reason to change.** If a class manages both `Person` data and `Address` data, it has two reasons to change. Split them.

```java
// Bad: Person knows about addresses
public class Person {
    private String name;
    private String streetName;
    private String city; // address concerns mixed in
}

// Good: each class has one responsibility
public class Person {
    private String name;
    private Address address;
}

public class Address {
    private String streetName;
    private String city;
}
```

**Encapsulate internal state.** Fields should be `private`, with behavior exposed through methods that enforce invariants:

```java
// Bad: anyone can set balance to -1000
public class BankAccount { public double balance; }

// Good: balance is protected by business rules
public class BankAccount {
    private double balance;
    public void deposit(double amount) { ... }
    public void withdraw(double amount) { ... }
}
```

---

## 7. SOLID Principles

### S — Single Responsibility
*A class should have only one reason to change.* (See Section 6.)

### O — Open/Closed
*Open for extension, closed for modification.* Add new behavior by creating new classes, not by modifying existing ones.

```java
// Bad: adding a new shape requires editing AreaCalculator
public double calculateArea(Object shape) {
    if (shape instanceof Circle) { ... }
    else if (shape instanceof Rectangle) { ... }
    // Must modify this to add Triangle
}

// Good: each shape implements its own calculateArea()
public interface Shape { double calculateArea(); }
public class Circle implements Shape { ... }
public class Rectangle implements Shape { ... }
public class Triangle implements Shape { ... } // no existing code touched
```

### L — Liskov Substitution
*Subclasses should be substitutable for their parent.* If `Ostrich extends Bird` throws on `fly()`, code expecting a `Bird` will break. Redesign the hierarchy — give `Bird` an abstract `move()` method instead.

### I — Interface Segregation
*Don't force clients to depend on interfaces they don't use.* Split a large `Worker` interface (with `work()`, `eat()`, `sleep()`) into focused ones (`Workable`, `Eatable`, `Sleepable`) so a `Robot` only needs to implement `Workable`.

### D — Dependency Inversion
*Depend on abstractions, not concrete implementations.* Inject dependencies rather than creating them inside a class.

```java
// Bad: UserService is tightly coupled to MySQLDatabase
public class UserService {
    private MySQLDatabase database = new MySQLDatabase();
}

// Good: UserService depends on an interface; implementation is injected
public class UserService {
    private Database database;
    public UserService(Database database) { this.database = database; }
}
```

---

## 8. Code Smells and Refactoring

A *code smell* is a sign that code could be cleaner — not a bug, but a design problem worth addressing.

| Smell | Fix |
|---|---|
| **Long Method** | Extract into smaller methods |
| **Large Class** | Split by responsibility |
| **Duplicate Code** | Extract shared logic into one place |
| **Magic Numbers** | Replace with named constants |
| **Long Parameter List** | Group into an object |
| **Dead Code** | Delete it |

**Example — eliminating magic numbers:**

```java
// Bad
if (price > 100) return price * 0.85;

// Good
private static final double PREMIUM_THRESHOLD = 100.0;
private static final double PREMIUM_RATE = 0.85;

if (price > PREMIUM_THRESHOLD) return price * PREMIUM_RATE;
```

---

## 9. Testing and Code Reviews

**Clean code is testable code.** Unit tests should be automated, repeatable, independent, and cover edge cases — not just the happy path.

```java
@Test
void calculateAreaReturnsCorrectValue() {
    RectangleCalculator calc = new RectangleCalculator();
    assertEquals(50.0, calc.calculateArea(5.0, 10.0), 0.001);
}
```

**Code reviews** enforce quality before code reaches the main branch. Focus on: coding standards, readability, correctness, maintainability, and edge-case handling.

---

## 10. Coding Conventions

Conventions are enforced by discipline and tooling (linters, formatters), not the compiler. Key Java conventions:

- **Braces:** always use them, even for single-line bodies
- **Indentation:** 4 spaces per level
- **One statement per line**
- **No wildcard imports** — group and sort imports explicitly
- **Class member order:** static constants → static fields → instance fields → constructors → public methods → private methods

Popular references: Google Java Style Guide, Oracle Java Code Conventions, Checkstyle.

---

## 11. Emergent Design and Concurrency

Write the simplest code that works, then refactor as requirements evolve. Avoid over-engineering upfront.

For concurrent code: avoid shared mutable state, and **prefer immutable objects** — they are inherently thread-safe.

```java
public final class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) { this.x = x; this.y = y; }

    public Point translate(int dx, int dy) {
        return new Point(x + dx, y + dy); // new object; original unchanged
    }
}
```

---

## Summary

1. **Write code for humans first.** Computers run it; people maintain it.
2. **Names matter.** Intention-revealing, pronounceable, searchable.
3. **Functions do one thing.** If you need "and" to describe it, split it.
4. **Let code speak for itself.** Minimize comments.
5. **Format consistently.** Follow your team's style guide.
6. **One class, one responsibility.** Apply SRP rigorously.
7. **Follow SOLID.** Especially DIP — inject dependencies through interfaces.
8. **Spot and fix code smells.** Magic numbers, duplication, long methods.
9. **Test and review.** These are professional responsibilities, not optional extras.

---

## Assignments

### Assignment 1: ConsoleLogger *(Required)*

Write a `ConsoleLogger` class that follows clean coding principles.

**Requirements:**
- A `log(String message)` method that prints with a timestamp: `[2024-01-15 14:30:45] Your message`
- Meaningful names, proper formatting, Javadoc on public methods

```java
public class ConsoleLogger {
    // Your implementation here
}
```

### Assignment 2: Using the Logger *(Required)*

Write a `UserRegistrationService` that uses `ConsoleLogger`.

**Requirements:**
- At least one method that performs an operation and logs its activity
- Apply DIP: depend on a `Logger` interface, not `ConsoleLogger` directly

### Assignment 3: Refactoring Exercise *(Optional)*

Refactor the following code to follow clean coding principles — apply meaningful names, SRP, extracted methods, and eliminate smells.

```java
public class D {
    public static void p(int[] a) {
        for (int i = 0; i < a.length; i++)
            for (int j = i + 1; j < a.length; j++)
                if (a[i] > a[j]) { int t = a[i]; a[i] = a[j]; a[j] = t; }
        for (int i = 0; i < a.length; i++) System.out.print(a[i] + " ");
        System.out.println();
        double s = 0;
        for (int i = 0; i < a.length; i++) s += a[i];
        System.out.println("Avg: " + s / a.length);
    }
}
```
