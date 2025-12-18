# Synchronizers

- [Synchronizers](#synchronizers)
  - [CountDownLatch](#countdownlatch)
    - [Timeout Scenarios](#timeout-scenarios)
  - [CyclicBarrier](#cyclicbarrier)
  - [Semaphore](#semaphore)
  - [Exchanger](#exchanger)
  - [Phaser](#phaser)


## CountDownLatch

Purpose: Makes threads wait until a set of operations completes.

One-time use only (cannot reset).

Key idea: You initialize it with a count, and threads call await() to wait. Other threads call countDown() to decrease the count.

```java
CountDownLatch latch = new CountDownLatch(3);

// Worker threads
Runnable worker = () -> {
    System.out.println(Thread.currentThread().getName() + " working");
    latch.countDown();
};

new Thread(worker).start();
new Thread(worker).start();
new Thread(worker).start();

// Main thread waits for workers
latch.await();
System.out.println("All workers done!");
```

Use-case: Waiting for multiple services to initialize before starting the main application.

### Timeout Scenarios

You can use await(timeout, TimeUnit) to wait for a limited time, and proceed if tasks take too long.

Useful for preventing indefinite blocking when waiting for multiple threads or services.

```java
if (latch.await(5, TimeUnit.SECONDS)) {
    System.out.println("All tasks completed in time");
} else {
    System.out.println("Timeout reached");
}
```

## CyclicBarrier

Purpose: Makes threads wait for each other at a barrier point.

Reusable (unlike CountDownLatch, can reset after use).

Key idea: Threads call await() and block until all threads reach the barrier.

```java
CyclicBarrier barrier = new CyclicBarrier(3, () -> System.out.println("All threads reached barrier"));

Runnable task = () -> {
    System.out.println(Thread.currentThread().getName() + " reached barrier");
    barrier.await(); // waits for others
};

new Thread(task).start();
new Thread(task).start();
new Thread(task).start();
```

Use-case: Multiplayer game rounds, batch processing where tasks synchronize at stages.

## Semaphore

Purpose: Controls access to a limited resource.

Key idea: Initialize with number of permits. Threads acquire() to enter and release() when done.

```java
Semaphore connections = new Semaphore(10);

if (connections.tryAcquire(3, TimeUnit.SECONDS)) {
    try {
        queryDatabase();
    } finally {
        connections.release();
    }
} else {
    throw new RuntimeException("DB overloaded");
}
```
Use-case: Limiting DB connections, thread pool slots, or printer access.

## Exchanger

Purpose: Allows two threads to exchange data.

Key idea: Each thread calls exchange() and waits for the other thread to arrive.

```java
Exchanger<String> exchanger = new Exchanger<>();

new Thread(() -> {
    try {
        String data = "Thread1 data";
        String received = exchanger.exchange(data);
        System.out.println("Thread1 received: " + received);
    } catch (InterruptedException e) { }
}).start();

new Thread(() -> {
    try {
        String data = "Thread2 data";
        String received = exchanger.exchange(data);
        System.out.println("Thread2 received: " + received);
    } catch (InterruptedException e) { }
}).start();
```

Use-case: Producer-consumer where threads swap buffers.

## Phaser

Purpose: More flexible barrier that supports multiple phases and dynamic threads.

Can register/deregister parties dynamically.

Useful for multi-phase computation.

```java
Phaser phaser = new Phaser(3); // 3 threads

Runnable task = () -> {
    System.out.println(Thread.currentThread().getName() + " Phase 1");
    phaser.arriveAndAwaitAdvance(); // wait for others
    System.out.println(Thread.currentThread().getName() + " Phase 2");
    phaser.arriveAndDeregister(); // finished
};

new Thread(task).start();
new Thread(task).start();
new Thread(task).start();
```

Use-case: Multi-stage simulations, pipelines, or iterative parallel tasks.
