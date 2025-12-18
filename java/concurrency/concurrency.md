# Concurrency

- [Concurrency](#concurrency)
  - [Virtual Threads](#virtual-threads)


## Virtual Threads

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
