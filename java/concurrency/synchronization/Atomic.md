# Atomic Variables

Atomic variables provide:

- Atomic operations (no partial updates)

- Visibility guarantees (like `volatile`)

- Lock-free, thread-safe updates

They are built on CAS (Compare-And-Swap).

``` java
compare currentValue with expectedValue
if equal → update to newValue
else → retry
```
This happens at the CPU level using special instructions.

No locks. No blocking.

## Common Atomic Classes

AtomicInteger

AtomicLong

AtomicBoolean

AtomicReference<T>

AtomicIntegerArray, AtomicLongArray

LongAdder, LongAccumulator (high contention)

## AtomicInteger / AtomicLong

Thread-safe counter

``` java

import java.util.concurrent.atomic.AtomicInteger;

class Counter {
    private final AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet();
    }

    public int get() {
        return count.get();
    }
}

get()
set(int value)
incrementAndGet()
getAndIncrement()
addAndGet(int delta)
compareAndSet(expected, update)

```
## AtomicReference

Used to atomically update object references

```java
import java.util.concurrent.atomic.AtomicReference;

class StateManager {
    private final AtomicReference<String> state =
            new AtomicReference<>("INIT");

    public void updateState(String newState) {
        state.compareAndSet("INIT", newState);
    }

    public String getState() {
        return state.get();
    }
}

```

With AtomicReference, `compareAndSet` guarantees a safe state transition,
but callers must rely on the return value. A separate `get()`
call can observe a different state due to races.

## Long Adder

### High Contention

Contention happens when many threads try to update the same shared variable at the same time.

```java 
With AtomicLong
AtomicLong counter = new AtomicLong();

counter.incrementAndGet();
```

Internally this uses CAS on one memory location.

Under heavy writes:

Many threads read the same value

All try CAS at once

Only one CAS succeeds

Others fail and retry

This creates CAS spinning

This is called high contention.

Why this hurts performance

Failed CAS operations waste CPU

Cache line ping-pong between cores

Performance drops as thread count increases

So paradoxically:

More threads → slower counter

### LongAdder (high contention counters)

AtomicLong can suffer contention under heavy writes

LongAdder spreads updates across multiple cells

```java
LongAdder counter = new LongAdder();
counter.increment();
long value = counter.sum();
```

Tradeoff

Faster under contention

sum() is not strictly atomic at an instant
