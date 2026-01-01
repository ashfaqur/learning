# Concurrency

- [Concurrency](#concurrency)
- [Contention](#contention)
- [ThreadLocal](#threadlocal)
  - [InheritableThreadLocal](#inheritablethreadlocal)
- [Virtual Threads](#virtual-threads)

# Contention

Contention occurs when multiple threads compete for the same shared resource, and only one can proceed at a time.

While one thread holds that resource, others:
- block
- spin-wait
- get descheduled
- retry work

This reduces throughput and increases latency — even if CPUs are idle — because threads are waiting instead of doing useful work.

Shared resources that commonly cause contention:
locks / monitors (synchronized)
ReentrantLocks
atomic variables
CPU cache lines (false sharing)
I/O handles
memory bandwidth
thread pools
database connections

# ThreadLocal

ThreadLocal lets each thread keep its own private copy of a variable,
even though all threads access the same ThreadLocal object.

So instead of sharing state across threads, each thread gets its own state.

```java
private static final ThreadLocal<Integer> localCounter =
        ThreadLocal.withInitial(() -> 0);

public static void increment() {
    localCounter.set(localCounter.get() + 1);
}

```

Thread A sees → 0,1,2  // A calls increment 2 times

Thread B sees → 0,1 // B calls 1 times

Thread C sees → 0 // C did not call increment

## InheritableThreadLocal

InheritableThreadLocal is a variant of ThreadLocal where a child thread automatically receives a copy of the parent thread’s value when it is created.

# Virtual Threads

Definition: Lightweight, JVM-managed threads introduced in Java 21 (Project Loom).

Purpose: Handle massive concurrency efficiently while allowing blocking-style code.

Key Differences from Traditional Threads:

Memory: Small, flexible stacks vs ~1 MB per OS thread.

Scalability: Millions of threads possible vs thousands.

Blocking I/O: Doesn’t block OS threads.

Scheduling: Managed by JVM instead of OS.

Use Case: High-concurrency I/O-heavy applications (servers, clients).

Benefit: Simplifies code—no reactive programming needed for scalable concurrency.

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 1_000_000; i++) {
        final int task = i;
        executor.submit(() -> {
            System.out.println("Task " + task + " running in " + Thread.currentThread());
            try { Thread.sleep(1000); } catch (InterruptedException e) {}
        });
    }
}
```
