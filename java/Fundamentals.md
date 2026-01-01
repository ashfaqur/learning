- [Object-Oriented Programming](#object-oriented-programming)
  - [Access Modifiers](#access-modifiers)
  - [Encapsulation](#encapsulation)
  - [Inheritance](#inheritance)
  - [Polymorphism](#polymorphism)
  - [Abstraction](#abstraction)
  - [Interfaces](#interfaces)
  - [Composition](#composition)
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
- [Java Language Features](#java-language-features)
  - [Autoboxing \& unboxing](#autoboxing--unboxing)
    - [Pitfalls](#pitfalls)
  - [Integer Cache](#integer-cache)
    - [Why does Java cache small integers?](#why-does-java-cache-small-integers)
  - [Generics (bounded/unbounded wildcards)](#generics-boundedunbounded-wildcards)
    - [Wildcards: ?](#wildcards-)
    - [Unbounded wildcard — List\<?\>](#unbounded-wildcard--list)
    - [Upper-bounded wildcard — \<? extends T\>](#upper-bounded-wildcard---extends-t)
    - [Lower-bounded wildcard — \<? super T\>](#lower-bounded-wildcard---super-t)

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

# Java Language Features

## Autoboxing & unboxing

Autoboxing is when Java automatically converts a primitive value into its corresponding wrapper object.

```java
int num = 10;
Integer boxed = num;   // autoboxing (int -> Integer)
```

Unboxing is the reverse conversion — from wrapper object → primitive.

```java
Integer value = 20;
int num = value;   // unboxing (Integer -> int)
```

It allows primitives to work with:

Collections (List<Integer>, not List<int>)

Generic types

Streams & functional APIs

```java
List<Integer> nums = List.of(1, 2, 3);
int x = nums.get(0);  // unboxing
```

### Pitfalls

NullPointerException in unboxing

```java
Integer x = null;
int y = x;   // ❌ NPE at runtime
```

Autoboxing affects ==

```java
Integer a = 128;
Integer b = 128;

System.out.println(a == b);   // false
System.out.println(a.equals(b)); // true
```

Performance overhead

```java
Integer sum = 0;
for (int i = 0; i < 1_000_000; i++) {
    sum += i;   // autoboxing each iteration (slow)
}
```

## Integer Cache

Java caches Integer objects in the range: -128 to 127

``` java
Integer a = 127;
Integer b = 127;

System.out.println(a == b);   // ✅ true (same cached object)
```
But outside that range:

```java
Integer x = 128;
Integer y = 128;

System.out.println(x == y);   // ❌ false (different objects)
```

👉 Always use .equals() for wrappers

### Why does Java cache small integers?

Because small numbers are used VERY frequently:

loop counters

array indexes

common values like 0, 1, -1

Caching:

avoids object creation overhead

improves performance

reduces memory use

## Generics (bounded/unbounded wildcards)

Generics allow classes and methods to work with types safely (compile-time type checking).

```java
List<String> names = new ArrayList<>();
names.add("Alice");   // only Strings allowed
```

### Wildcards: ?

A wildcard means:

“I don’t know the exact type, but it’s some type.”

```java
List<?> items;
```
You can read from it, but cannot add to it (except null).

Think of ? as: read-only unknown type

### Unbounded wildcard — List<?>

Used when the type doesn’t matter.

```java
void printList(List<?> list) {
    for (Object o : list) {
        System.out.println(o);
    }
}

printList(List.of(1,2,3));
printList(List.of("a","b"));
```

Use when: method only reads and no insertions needed

### Upper-bounded wildcard — <? extends T>

Means: “any subtype of T”

```java
List<? extends Number> nums;

void sum(List<? extends Number> nums) {
    for (Number n : nums) {
        System.out.println(n.doubleValue());
    }
}
```

Cannot add elements:

```java
nums.add(10);   // ❌ not allowed
```

Because we don’t know whether it is:

List<Integer> ?

List<Double> ?

Compiler blocks it.

### Lower-bounded wildcard — <? super T>

Means: “any supertype of T”

```java
List<? super Integer> list;
```

Valid:

List<Integer>

List<Number>

List<Object>

Can add

```java
list.add(10);   // ✅ allowed
```

But reading returns Object:

```java
Object value = list.get(0);
```
