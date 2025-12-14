# Thread Priority And Groups

# Thread Priority

Thread priority is a hint to the thread scheduler about the relative importance of a thread.


```java
Thread.MIN_PRIORITY  // 1
Thread.NORM_PRIORITY // 5 (default)
Thread.MAX_PRIORITY  // 10


Thread t = new Thread(() -> doWork());
t.setPriority(Thread.MAX_PRIORITY);
t.start();
```

Thread priority is only a scheduling hint and should never be relied upon for correctness because actual scheduling is OS-dependent.

Use proper concurrency tools instead:
ExecutorService,
Blocking queues,
Locks,
Semaphores

## Thread Groups

A ThreadGroup is a logical grouping of threads.


```java
ThreadGroup group = new ThreadGroup("MyGroup");

Thread t1 = new Thread(group, () -> doWork(), "worker-1");
Thread t2 = new Thread(group, () -> doWork(), "worker-2");

t1.start();
t2.start();


group.interrupt();

group.setMaxPriority(Thread.NORM_PRIORITY);
```

ThreadGroup exists for grouping and managing threads, but it’s largely obsolete and superseded by ExecutorService and modern concurrency utilities.
