- [SOLID Principles](#solid-principles)
  - [S — Single Responsibility Principle (SRP)](#s--single-responsibility-principle-srp)
  - [O — Open/Closed Principle (OCP)](#o--openclosed-principle-ocp)
  - [L — Liskov Substitution Principle (LSP)](#l--liskov-substitution-principle-lsp)
  - [I — Interface Segregation Principle (ISP)](#i--interface-segregation-principle-isp)
  - [D — Dependency Inversion Principle (DIP)](#d--dependency-inversion-principle-dip)
- [Singleton](#singleton)
  - [Singleton Lazy Holder](#singleton-lazy-holder)
  - [Singleton Enum](#singleton-enum)
- [Factory / Factory Method Pattern](#factory--factory-method-pattern)
  - [Why use it / Purpose:](#why-use-it--purpose)
  - [When to use:](#when-to-use)
  - [Code](#code)
- [Builder Design Pattern](#builder-design-pattern)
  - [Why / When to Use:](#why--when-to-use)
  - [Code](#code-1)
- [Strategy Design Pattern](#strategy-design-pattern)
  - [Why / When to Use:](#why--when-to-use-1)
- [Observer Pattern](#observer-pattern)
  - [When is it used](#when-is-it-used)
- [Decorator Pattern](#decorator-pattern)
  - [Why / When to Use:](#why--when-to-use-2)
  - [Code](#code-2)
- [Proxy Pattern](#proxy-pattern)
  - [When to Use:](#when-to-use-1)
- [Template Method Pattern](#template-method-pattern)
  - [Use Template Method when:](#use-template-method-when)
  - [Code](#code-3)



# SOLID Principles

## S — Single Responsibility Principle (SRP)

One class, one job

A class should have logic for single responsibility

BAD

``` java
class User {
    void saveToDatabase() { /* DB logic */ }
    void sendEmail() { /* Email logic */ }
}
```

GOOD

```java
class User {
    private String name;
    private String email;
    // only user data
}

class UserRepository {
    void save(User user) { /* DB logic */ }
}

class EmailService {
    void sendEmail(User user, String message) { /* Email logic */ }
}
```

## O — Open/Closed Principle (OCP)

Extend, don’t modify.

Software entities should be open for extension, but closed for modification.

BAD

```java
class PaymentProcessor {
    void pay(String type) {
        if (type.equals("CreditCard")) {
            System.out.println("Paying with credit card");
        } else if (type.equals("PayPal")) {
            System.out.println("Paying with PayPal");
        }
    }
}
```

Problem: Every time you add a new payment method, you modify the class → violates OCP.

GOOD

```java
interface PaymentMethod {
    void pay();
}

class CreditCardPayment implements PaymentMethod {
    public void pay() {
        System.out.println("Paying with credit card");
    }
}

class PayPalPayment implements PaymentMethod {
    public void pay() {
        System.out.println("Paying with PayPal");
    }
}

class PaymentProcessor {
    void process(PaymentMethod method) {
        method.pay();  // open for extension: new payment methods can be added
    }
}

```

Adding a new payment type now doesn’t require changing PaymentProcessor.

You just create a new class implementing PaymentMethod.

## L — Liskov Substitution Principle (LSP)

Substitute safely.

Subtypes must be replaceable with their base types without altering correctness.

BAD

```java
class Bird {
    void fly() { System.out.println("Flying"); }
}

class Ostrich extends Bird {
    @Override
    void fly() { throw new UnsupportedOperationException(); }
}

Violates LSP — ostriches can’t fly.

```

GOOD

```java
interface Bird {}
interface Flyable { void fly(); }

class Sparrow implements Bird, Flyable {
    public void fly() { System.out.println("Flying"); }
}

class Ostrich implements Bird {
    // does not implement Flyable
}

```

## I — Interface Segregation Principle (ISP)

Don’t force extra work.

Clients should not be forced to depend on interfaces they don’t use.

BAD

```java
interface Worker {
    void work();
    void eat();
}

class Robot implements Worker {
    public void work() { /* work */ }
    public void eat() { throw new UnsupportedOperationException(); }
}
```

GOOD

```java
interface Workable {
    void work();
}
interface Eatable {
    void eat();
}

class Human implements Workable, Eatable { ... }
class Robot implements Workable { ... }
```

## D — Dependency Inversion Principle (DIP)

Depend on a contract, not a concrete class.

Don’t let the high-level code directly depend on low-level details.
Both should depend on an interface (or abstraction).

BAD

```java
class MySQLDatabase {
    void connect() { ... }
}
class UserService {
    private MySQLDatabase db = new MySQLDatabase(); // tightly coupled
}
```

GOOD

```java
interface Database {
    void connect();
}
class MySQLDatabase implements Database { ... }
class PostgreSQLDatabase implements Database { ... }

class UserService {
    private Database db;
    UserService(Database db) {
        this.db = db; // injected
    } 
}
```
UserService (high-level) doesn’t care if it’s MySQL or PostgreSQL.

MySQLDatabase (low-level) just implements the Database interface.

Both depend on the abstraction (Database).

# Singleton

The Singleton pattern is a creational design pattern that ensures a class has only one instance and provides a global point of access to that instance.

Key Points:
- Only one object of the class exists throughout the application.
- The instance is globally accessible via a public method (usually getInstance()).
- Can be lazy-loaded (created on demand) or eagerly initialized (created at class load).

Typical Use Cases:
- Configuration managers
- Logging frameworks
- Database connection pools
- Caches

## Singleton Lazy Holder

``` java
public class SingletonLazyHolder {

    private static class LazyHolder {
        private static final SingletonLazyHolder INSTANCE = new SingletonLazyHolder();
    }

    private SingletonLazyHolder() {}

    public static SingletonLazyHolder getInstance() {
        return LazyHolder.INSTANCE;
    }
}
```

## Singleton Enum

``` java
public enum SingletonEnum {
    INSTANCE;
}
```

# Factory / Factory Method Pattern

The Factory Method is a creational design pattern that defines an interface for creating objects, but lets subclasses decide which class to instantiate.

In other words, it encapsulates object creation so the client code doesn’t need to know the exact class being instantiated.

## Why use it / Purpose:
- To decouple object creation from usage
- To make code flexible and extensible without changing existing code
- To support polymorphism: you can work with interfaces or abstract classes instead of concrete classes

## When to use:
- When the exact type of object is unknown at compile time
- When you want to avoid tight coupling between client and concrete classesWhen you need easy extensibility — e.g., adding new product types without modifying client code

## Code

```java
// Product interface
interface IShape {
    void draw();
}

// Concrete Products
class Circle implements IShape {
    public void draw() {
        System.out.println("Drawing Circle"); 
    }
}
class Rectangle implements IShape {
    public void draw() {
        System.out.println("Drawing Rectangle");
        }
}

// Factory
class ShapeFactory {
    public static IShape getShape(String type) {
        switch(type) {
            case "circle":
                return new Circle();
            case "rectangle":
                return new Rectangle();
            default:
                throw new IllegalArgumentException("Unknown shape");
        }
    }
}

// Client

IShape shape1 = ShapeFactory.getShape("circle");
shape1.draw();
IShape shape2 = ShapeFactory.getShape("rectangle");
shape2.draw();
```

Client only depends on Shape interface, not concrete classes

Easy to add new shapes without changing client code

# Builder Design Pattern

The Builder pattern is a creational design pattern that separates the construction of a complex object from its representation, allowing the same construction process to create different representations.

In simpler terms: it helps build objects step by step, especially useful when a class has many optional parameters or complex construction logic.

## Why / When to Use:
- When a class has many fields (especially optional ones) → avoids telescoping constructors
- When you want immutable objects
- When object creation involves complex steps

## Code

```java
// Product class
public class Pizza {
    private String size;         // required
    private boolean cheese;      // optional
    private boolean pepperoni;   // optional

    private Pizza(Builder builder) {
        this.size = builder.size;
        this.cheese = builder.cheese;
        this.pepperoni = builder.pepperoni;
    }

    public static class Builder {
        private final String size; // required
        private boolean cheese = false;
        private boolean pepperoni = false;

        public Builder(String size) {
            this.size = size;
        }

        public Builder cheese(boolean value) {
            this.cheese = value; return this;
        }
        public Builder pepperoni(boolean value) {
            this.pepperoni = value; return this;
        }
        public Pizza build() {
            return new Pizza(this);
        }
    }

    @Override
    public String toString() {
        return "Pizza [size=" + size + ", cheese=" + cheese +
               ", pepperoni=" + pepperoni + "]";
    }
}

// Client

Pizza pizza = new Pizza.Builder("Large")
                    .cheese(true)
                    .pepperoni(true)
                    .build();
System.out.println(pizza);

}

```
Key Points / Interview Notes:
- Builder avoids complex constructors and improves readability
- Supports immutable objects
- Often used in Java APIs: StringBuilder, java.time.LocalDateTime, Stream.Builder
- Fluent API style → chaining method calls


# Strategy Design Pattern

The Strategy pattern is a behavioral design pattern that defines a family of algorithms, encapsulates each one, and makes them interchangeable.

It allows the algorithm to vary independently from clients that use it.

In simpler words: it lets you swap behavior at runtime without changing the code that uses it.

## Why / When to Use:
- When you have multiple ways to perform an action (algorithms or behaviors)
- When you want to avoid if/else or switch statements
- When you want flexibility and extensibility: add new behavior without modifying existing code


```java
// Strategy interface
interface IPaymentStrategy {
    void pay(int amount);
}

// Concrete strategies
class CreditCardPayment implements IPaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using Credit Card");
    }
}

class PayPalPayment implements IPaymentStrategy {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " using PayPal");
    }
}

// Context
class ShoppingCart {
    private IPaymentStrategy paymentStrategy;

    public ShoppingCart(IPaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void checkout(int amount) {
        paymentStrategy.pay(amount);
    }
}

ShoppingCart cart1 = new ShoppingCart(new CreditCardPayment());
cart1.checkout(500);

ShoppingCart cart2 = new ShoppingCart(new PayPalPayment());
cart2.checkout(300);
}

```

# Observer Pattern

The Observer pattern is a behavioral design pattern where:
- One object is the Subject / Publisher
- Other objects are Observers / Subscribers
- Observers register to receive updates
- When the subject’s state changes → it notifies all observers

In short:

“One-to-many notification when state changes.”

## When is it used

- Many objects need to react to a single event or state change
- You want to avoid tight coupling between components
- You need an event-driven system

Typical real-world uses:
- UI / GUI event listeners
- Messaging systems
- Reactive streams
- Notification systems
- Domain events
- Webhooks

Java & Spring examples:
- PropertyChangeListener
- EventListener / ApplicationEventPublisher
- Reactive programming (Flux, Mono)
- Kafka / RabbitMQ consumers

```java
interface IClickListener {
    void onClick();
}

class Button {

    private final List<IClickListener> listeners = new ArrayList<>();

    public void addClickListener(IClickListener listener) {
        listeners.add(listener);
    }

    public void removeClickListener(IClickListener listener) {
        listeners.remove(listener);
    }

    public void click() {
        System.out.println("Button clicked");
        for (IClickListener listener : listeners) {
            listener.onClick();
        }
    }
}

class SoundEffectListener implements IClickListener {
    public void onClick() {
        System.out.println("Play click sound");
    }
}

class AnalyticsListener implements IClickListener {
    public void onClick() {
        System.out.println("Send analytics event");
    }
}

Button button = new Button();

button.addClickListener(new SoundEffectListener());
button.addClickListener(new AnalyticsListener());

button.click();
```

# Decorator Pattern

The Decorator pattern is a structural design pattern that adds new behavior to objects dynamically without changing their class.

It works by wrapping an existing object in a decorator class that implements the same interface, allowing behaviors to be stacked or combined at runtime.

Decorator wraps an object and adds extra behavior, keeping the same interface.

## Why / When to Use:
- When you want to add functionality without modifying existing classes
- When subclassing is impractical or leads to class explosion
- To implement flexible, runtime-extensible features

## Code

```java
// Component interface
interface ICoffee {
    double cost();
    String description();
}

// Concrete Component
class SimpleCoffee implements ICoffee {
    public double cost() {
        return 2.0;
    }
    public String description() {
        return "Simple Coffee";
    }
}

// Decorator abstract class
abstract class CoffeeDecorator implements ICoffee {
    protected ICoffee coffee;

    public CoffeeDecorator(ICoffee coffee) {
        this.coffee = coffee;
    }

    public double cost() {
        return this.coffee.cost();
    }

    public String description() {
        return this.coffee.description();
    }
}

// Concrete Decorators
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(ICoffee coffee) {
        super(coffee);
    }

    @Override
    public double cost() {
        return super.cost() + 0.5;
    }

    @Override
    public String description() {
        return super.description() + ", Milk";
    }
}

class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(ICoffee coffee) {
        super(coffee);
    }

    @Override
    public double cost() {
        return super.cost() + 0.2;
    }

    @Override
    public String description() {
        return super.description() + ", Sugar";
    }
}

// Client
ICoffee simpleCoffee = new SimpleCoffee();                  // Base object
ICoffee milkCoffee = new MilkDecorator(simpleCoffee);       // Add milk
ICoffee milkSugarCoffee = new SugarDecorator(milkCoffee);   // Add sugar
``` 

Key Points:
- Decorator wraps objects, not subclasses them
- Multiple decorators can be stacked dynamically
- Follows Open/Closed Principle (add behavior without modifying original class)
- Common real-world examples in Java:
    - java.io.InputStream / BufferedInputStream / DataInputStream
    - java.io.OutputStream / PrintStream wrappers
    - Logging frameworks (wrapping loggers with filters or formatters)


# Proxy Pattern

The Proxy pattern is a structural design pattern that provides a surrogate or placeholder for another object to control access to it.
- Implements the same interface as the real object
- Controls when, how, or whether the real object is accessed
- Can add extra behavior (lazy loading, caching, logging, security)

## When to Use:
- Lazy initialization / Virtual Proxy — delay expensive object creation until needed
- Access control / Protection Proxy — restrict access based on permissions
- Logging / Auditing — track method calls
- Remote Proxy — represent an object in a different address space (e.g., RMI)

Proxy acts as an intermediary between the client and the real object.

```java
// Subject interface
interface IImage {
    void display();
}

// Real object
class RealImage implements IImage {
    private String filename;

    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk();
    }

    private void loadFromDisk() {
        System.out.println("Loading " + filename);
    }

    public void display() {
        System.out.println("Displaying " + filename);
    }
}

// Proxy
class ProxyImage implements IImage {
    private RealImage realImage;
    private String filename;

    public ProxyImage(String filename) {
        this.filename = filename;
    }

    public void display() {
        if (realImage == null) {             // Lazy loading
            realImage = new RealImage(filename);
        }
        realImage.display();
    }
}

// Client
IImage image = new ProxyImage("photo.jpg");

// Image will be loaded only when display is called
image.display();  // Loads from disk and displays
image.display();  // Displays without loading again
```

# Template Method Pattern

The Template Method is a behavioral design pattern where:
- A base class defines the overall algorithm (the “template”)
- Subclasses provide specific implementations for selected steps

The parent (abstract class) defines the base algorithm / shared behavior,
while child classes override only the specific steps.


## Use Template Method when:
- You have an algorithm with common steps + variable steps
- Subclasses should control only certain parts of the process
- You want to avoid code duplication across similar classes

Common areas:
- Frameworks & libraries
- Data processing pipelines
- Game engines (turn sequences)
- Workflow processing
- File import/export flows

## Code

```java
abstract class BeverageMaker {

    // Template method (final: cannot be overridden)
    public final void prepareBeverage() {
        boilWater();
        brew();
        pourIntoCup();
        addCondiments();
    }

    private void boilWater() {
        System.out.println("Boiling water");
    }

    private void pourIntoCup() {
        System.out.println("Pouring into cup");
    }

    // Steps to be customized by subclasses
    protected abstract void brew();
    protected abstract void addCondiments();
}

```
