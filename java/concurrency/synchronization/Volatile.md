# Volatile Keyword

`volatile` guarantees visibility and ordering but not atomicity. It’s best used for simple state flags where one thread writes and others read.

It ensures that:

Reads always see the latest value written by any thread

Writes are immediately visible to other threads

Certain reorderings are prevented (happens-before guarantee)

What volatile guarantees

✅ Visibility

Without volatile, threads may cache variables locally (CPU cache, registers).

With volatile:

`volatile boolean running = true;`


If one thread updates running = false,

all other threads will see it immediately.

✅ Happens-before relationship

A write to a volatile variable:

`running = false;`


happens-before every subsequent read of that variable.

This prevents instruction reordering around volatile reads/writes.

What volatile does NOT guarantee

❌ Atomicity

Operations like:

`count++;`

are not atomic, even if count is volatile.

Because `count++` = read → increment → write (3 steps).

❌ Mutual exclusion

Multiple threads can still execute code concurrently.

volatile does not block threads.

🔍 Example: Correct use of volatile

Stop flag pattern

```java
class Worker implements Runnable {
    private volatile boolean running = true;

    public void run() {
        while (running) {
            // do work
        }
    }

    public void stop() {
        running = false;
    }
}
```

## Why volatile is needed:

Without volatile, the loop may never exit

The thread might cache running as true

🔍 Example: Incorrect use of volatile

```java
volatile int count = 0;

public void increment() {
    count++; // NOT thread-safe
}
```

Fix options:

Use synchronized

Use AtomicInteger


## When to use volatile

✅ Use when:

One thread writes, others read

Simple flags / state

No compound operations

No invariants across multiple variables

❌ Don’t use when:

Multiple threads update the value

You need atomic read-modify-write

Multiple variables must stay consistent
