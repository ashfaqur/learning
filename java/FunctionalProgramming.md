
- [Lamdas](#lamdas)
- [Method References](#method-references)
- [Streams](#streams)
- [Optional](#optional)
  - [Optional.get()](#optionalget)
  - [Optional.orElse(T other)](#optionalorelset-other)
  - [Optional.orElseThrow()](#optionalorelsethrow)


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
