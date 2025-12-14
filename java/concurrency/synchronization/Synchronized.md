# Synchronized Keyword

`synchronized` provides mutual exclusion and visibility guarantees.

It ensures:

Only one thread at a time executes the synchronized code for a given lock

Memory visibility — changes made by one thread become visible to others when the lock is released

Under the hood, it uses a monitor lock (intrinsic lock).

## What object is locked?

Instance method → locks the current object (this)

Static method → locks the Class object

Synchronized block → locks the specified object


## Method-level synchronization

### Instance method synchronization

```java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

What’s locked?

The Counter instance (this)

Behavior:

Only one thread at a time can execute any synchronized instance method on the same object

Threads accessing different instances do not block each other

### Static method synchronization

```java
class Counter {
    private static int count = 0;

    public static synchronized void increment() {
        count++;
    }
}
```

What’s locked?

The Counter.class object

Behavior:

Only one thread across the entire JVM can enter this method at a time (per classloader)

Pros / Cons of method-level sync

Pros:

Simple

Easy to reason about

Cons:

Coarse-grained locking

Synchronizes the entire method, even if only part needs protection

Can hurt performance

## Block-level synchronization

Block-level synchronization lets you synchronize only the critical section.

```java
class Counter {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        // non-critical work

        synchronized (lock) {
            count++;
        }

        // more non-critical work
    }
}
```

What’s locked?

The object inside `synchronized(lock)`

Behavior:

Only the block is mutually exclusive

Other code in the method can run concurrently

Synchronizing on this

```java
public void increment() {
    synchronized (this) {
        count++;
    }
}
```

This is equivalent to a synchronized instance method — but explicit.

Pros / Cons of block-level sync

Pros:

Fine-grained control

Better performance

Can use different locks for different data

Cons:

Slightly more complex

Risk of locking on the wrong object


## Reentrancy

A thread holding a lock can re-enter it.


``` java
public synchronized void outer() {
    inner();
}

public synchronized void inner() {
    // works fine
}
```

