- [Object-Oriented Programming](#object-oriented-programming)
  - [Access Modifiers](#access-modifiers)
  - [Encapsulation](#encapsulation)
  - [Inheritance](#inheritance)
  - [Polymorphism](#polymorphism)
  - [Abstraction](#abstraction)
  - [Interfaces](#interfaces)
  - [Composition](#composition)
  - [SOLID Principles](#solid-principles)
    - [S — Single Responsibility Principle (SRP)](#s--single-responsibility-principle-srp)
    - [O — Open/Closed Principle (OCP)](#o--openclosed-principle-ocp)
    - [L — Liskov Substitution Principle (LSP)](#l--liskov-substitution-principle-lsp)
    - [I — Interface Segregation Principle (ISP)](#i--interface-segregation-principle-isp)
    - [D — Dependency Inversion Principle (DIP)](#d--dependency-inversion-principle-dip)
  - [Object equality (== vs equals / hashCode)](#object-equality--vs-equals--hashcode)
    - [== Operator:](#-operator)
    - [equals() Method:](#equals-method)
    - [hashCode() Method:](#hashcode-method)
- [Immutable Objects](#immutable-objects)
  - [String](#string)
  - [Records](#records)
  - [Builders](#builders)
  - [How to Make a Class Immutable](#how-to-make-a-class-immutable)
- [Data Transfer Object (DTO)](#data-transfer-object-dto)
- [Lamda And Streams](#lamda-and-streams)
  - [Lamdas](#lamdas)
  - [Method References](#method-references)
  - [Streams](#streams)
  - [Optional](#optional)
    - [Optional.get()](#optionalget)
    - [Optional.orElse(T other)](#optionalorelset-other)
    - [Optional.orElseThrow()](#optionalorelsethrow)

# Object-Oriented Programming

## Access Modifiers

Access modifiers control visibility of classes, methods, and fields.

```java
public class Person {
    private String name;          // only within Person
    int age;                       // default, package-only
    protected String address;       // package + subclasses
    public String country;          // accessible everywhere
}
```

Packages:

Folders that group related classes, help control access with default & protected.

## Encapsulation

HIDE = Hide data, Interact via methods

Hide fields → make them private

Interact using getters / setters

Define validation rules inside methods

Encapsulate behavior + data together

```java
public class User {
    private int age; // hidden data

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        if (age >= 0) {      // rule enforced here
            this.age = age;
        }
    }
}
```


## Inheritance

REUSE = Reuse code, Extend behavior

Reuse parent properties & methods

Extend by adding new features

Use `extends` keyword

Share common behavior

Enable overriding

```java
class Animal {
    void eat() {
        System.out.println("Animal eats");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("Dog barks");
    }
}

Dog d = new Dog();
d.eat();   // inherited from Animal
d.bark();  // Dog behavior
```

## Polymorphism

POLY = Many, MORPH = Forms

Same method but different behavior

Overloading (compile time)-> same method with different signature

Override (runtime) -> parent method overridden

``` java
interface IAnimal {
    void speak();  // contract
}

class Dog implements IAnimal {
    @Override
    public void speak() {
        System.out.println("Dog barks");
    }
}

class Cat implements IAnimal {
    @Override
    public void speak() {
        System.out.println("Cat meows");
    }
}

IAnimal a;

a = new Dog();
a.speak();  // Dog barks

a = new Cat();
a.speak();  // Cat meows
}

public void makeItSpeak(Animal a) {
    a.speak();  // works for any Animal implementation
}

```

## Abstraction

An abstract class or interface acts as a blueprint/parent.

It can:

Define essential methods that every subclass must implement (abstract methods).

Provide shared logic that all subclasses can use directly (concrete methods in abstract class).

Subclasses override abstract methods or use the provided concrete methods.

The user of the class only sees the exposed methods, not the internal workings of each subclass.

```java
abstract class Vehicle {
    abstract void start();  // no implementation here

    void stop() {           // implementation provided
        System.out.println("Vehicle stopped");
    }
}

class Car extends Vehicle {
    @Override
    void start() {
        System.out.println("Car started");
    }
}

Vehicle v = new Car();
v.start();  // Car started
v.stop();   // Vehicle stopped

```

## Interfaces

Can have only method signatures (before Java 8).

From Java 8+, can have default and static methods.

Cannot have instance variables (can have public static final constants).

Supports multiple inheritance (implements) — a class can implement many interfaces.

```java
interface Animal {
    void speak();  // must be implemented
    default void sleep() {
        System.out.println("Animal sleeps");
    }
}

class Dog implements Animal {
    @Override
    public void speak() {
        System.out.println("Dog barks");
    }
}

```

## Composition

Pass or contain objects inside a class and delegate behavior.

Inheritance = IS-A

Composition = HAS-A

```java
class Engine {
    void start() {}
}

class Car {
    private Engine engine;   // HAS-A relationship

    // Inject Engine during initialization
    Car(Engine engine) {
        this.engine = engine;
    }

    void drive() {
        engine.start();      // delegate behavior
    }
}

Engine e = new Engine();
Car car = new Car(e); // composition: Car HAS-A Engine
car.drive();

```

## SOLID Principles

### S — Single Responsibility Principle (SRP)

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

### O — Open/Closed Principle (OCP)

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

### L — Liskov Substitution Principle (LSP)

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

### I — Interface Segregation Principle (ISP)

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

### D — Dependency Inversion Principle (DIP)

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

## Object equality (== vs equals / hashCode)

### == Operator:

Checks if two references point to the same object in memory.

Does not compare content of objects.

Use it for primitives or checking if two references are literally the same object

```java
String s1 = new String("hello");
String s2 = new String("hello");

System.out.println(s1 == s2);  // false, different objects
System.out.println(s1.equals(s2)); // true, content is same
```

### equals() Method:

Compares logical equality (content) of objects.

```java
class Person {
    String name;
    int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Person p = (Person) obj;
        return age == p.age && name.equals(p.name);
    }

    @Override
    public int hashCode() {
        return name.hashCode() + age;
    }
}
```
### hashCode() Method:

Provides an integer representation of an object, used in hash-based collections (HashMap, HashSet, HashTable).

Contract with equals():

If two objects are equal according to equals(), they must have the same hashCode().

If two objects have the same hashCode(), they may or may not be equal according to equals().

# Immutable Objects

An object whose state cannot be changed after it is created.

Once an immutable object is created, all its fields stay the same.

Any “modification” creates a new object.

Benefits:
- Thread-safe by default (no synchronization needed).
- Easier to reason about — objects don’t change unexpectedly.
- Can be safely shared across different parts of a program.

## String

```java
String s1 = "Hello";
String s2 = s1.toUpperCase();  // creates a new String
System.out.println(s1); // Hello
System.out.println(s2); // HELLO
```

## Records

Records are immutable data carriers by default.

Fields are final, and the compiler generates getters.

```java
record Person(String name, int age) {}

Person p1 = new Person("Alice", 25);
// p1.name = "Bob";  // ❌ Compilation error
Person p2 = new Person("Bob", 25); // new object
```

## Builders

Builders are mutable, but used to construct immutable objects.

```java

// Immutable Car record
public record Car(String model, int year) {}

public static class CarBuilder {
    private String model;
    private int year;
    public CarBuilder setModel(String model) {
        this.model = model;
        return this;
    }
    public CarBuilder setYear(int year) {
        this.year = year;
        return this;
    }
    public Car build() {
        return new Car(model, year); // create immutable record
    }
}

Car car = new Car.CarBuilder()
                .setModel("Tesla")
                .setYear(2025)
                .build();
```

Car is now a record → automatically immutable, final, with getters.

Builder is still mutable → helps step-by-step construction.

No setters are needed in the record itself.

## How to Make a Class Immutable

Declare the class as final (prevents subclassing).

Make all fields private and final.

Provide no setters, only getters.

If a field is a mutable object, return a copy in getter.

```java
final class Student {
    private final String name;
    private final int age;
    private final List<String> courses;  // mutable object

    public Student(String name, int age, List<String> courses) {
        this.name = name;
        this.age = age;
        // Create a defensive copy to prevent external modification
        this.courses = List.copyOf(courses); // immutable copy
    }

    public String getName() { return name; }
    public int getAge() { return age; }

    // Return an unmodifiable view to prevent modification from outside
    public List<String> getCourses() {
        return courses;
    }
}

```


# Data Transfer Object (DTO)

A DTO is a simple Java object used to transfer data between layers or systems without containing business logic.

Encapsulates data only (no business logic).

Reduces network calls by bundling multiple fields.

Decouples layers (e.g., database from API).

Often serializable for remote communication.

Can hide sensitive information (e.g., don’t expose passwords).

```java
public class UserDTO {
    private String username;
    private String email;
    // getters/setters
}
```

Use Cases

API responses

Cross-system communication

Decoupling persistence and presentation layers

Tips

Use records in Java 16+ for brevity.

Mapping libraries like MapStruct or ModelMapper can automate conversions.

# Lamda And Streams

## Lamdas

## Method References

## Streams

Streams allow writing complex operations in a declarative style instead of verbose loops.

```java
List<String> names = List.of("Alice", "Bob", "Charlie");
List<String> filtered = names.stream()
                             .filter(name -> name.startsWith("A"))
                             .collect(Collectors.toList());
```

This is much cleaner than using traditional for loops with conditional checks.

1. Easier Parallelization

Streams support parallel processing out-of-the-box with parallelStream().

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);
int sum = numbers.parallelStream().mapToInt(Integer::intValue).sum();
```

Behind the scenes, Java handles thread management, splitting work across CPU cores, reducing the effort to write multi-threaded code.

2. Functional Style Encourages Immutability

Functional programming discourages mutable shared state.

Operations like map, filter, reduce do not change the original collection.

This reduces side effects and makes code more predictable.

3. Chaining and Composability

Stream operations can be chained fluently, which makes the logic easier to follow.

```java
int totalLength = names.stream()
                       .filter(name -> name.length() > 3)
                       .mapToInt(String::length)
                       .sum();
```

Each operation transforms data in a clear pipeline.

4. Powerful Data Processing

Streams provide high-level operations like:

filter – select elements

map – transform elements

flatMap – flatten nested structures

reduce – aggregate data

collect – gather results into lists, sets, maps

This enables complex data manipulation in fewer lines of code.

5. Lazy Evaluation

Intermediate operations (filter, map) are lazy and only executed when a terminal operation (collect, sum, forEach) is invoked.

This improves performance by avoiding unnecessary computations.

6. Encourages Declarative Programming

Focuses on what to do rather than how to do it.

Example: “Give me all names starting with A” vs looping manually.

7. Integration with Optional

Streams integrate well with Optional, allowing safe handling of absent values without explicit null checks.

```java
Optional<String> name = names.stream().filter(n -> n.startsWith("Z")).findFirst();
```

Summary:

Streams and functional programming in Java make code more concise, readable, and maintainable, provide easy parallelism, support immutable and declarative programming, and offer powerful, composable data operations.

## Optional

### Optional.get()

Purpose: Returns the value inside the Optional.

Behavior:

If a value is present → returns it.

If empty → throws NoSuchElementException.

Use carefully: Only call if you are sure the value is present.

```java
Optional<String> opt = Optional.of("Hello");
String val = opt.get(); // returns "Hello"

Optional<String> emptyOpt = Optional.empty();
emptyOpt.get(); // throws NoSuchElementException

```

### Optional.orElse(T other)

Purpose: Returns the value if present; otherwise, returns the provided default value.

Behavior: Safe alternative to get(). No exception thrown.


```java
Optional<String> opt = Optional.of("Hello");
String val = opt.orElse("Default"); // returns "Hello"

Optional<String> emptyOpt = Optional.empty();
String val2 = emptyOpt.orElse("Default"); // returns "Default"
```

Important: The argument other is evaluated eagerly, even if the Optional has a value.

### Optional.orElseThrow()

Purpose: Returns the value if present; otherwise, throws an exception.

Behavior:

By default: throws NoSuchElementException.

You can also provide a custom exception with orElseThrow(Supplier<? extends X> exceptionSupplier).

```java
Optional<String> opt = Optional.of("Hello");
String val = opt.orElseThrow(); // returns "Hello"

Optional<String> emptyOpt = Optional.empty();
emptyOpt.orElseThrow(); // throws NoSuchElementException

// Custom exception
emptyOpt.orElseThrow(() -> new IllegalArgumentException("Value missing"));
```
