# Executors

## Java ExecutorService

submit(Runnable)

Submit task without result → returns Future<?> (get() → null)

submit(Callable<T>)

Submit task with result → returns Future<T>

shutdown()

Graceful shutdown; no new tasks, existing tasks finish

shutdownNow()

Force shutdown; interrupts running tasks, returns not-started tasks

awaitTermination(timeout)

Waits for executor to terminate after shutdown; true if finished

invokeAll(tasks)

Runs all callables, waits for all to finish, returns List<Future<T>>
Use when all results matter (batch/parallel processing)

invokeAny(tasks)

Runs all callables, returns first successful result, cancels others
Use when fastest valid result matters (racing/failover)

Rule of thumb:

Need every result → invokeAll

Need any fast result → invokeAny


```java
ExecutorService executor = Executors.newFixedThreadPool(4);
Future<Integer> future = executor.submit(() -> 42);
future.get(); // blocks
executor.shutdown();
```

Handle exceptions inside fire-and-forget tasks; propagate exceptions with Future.get() only when the caller needs to react.

## ScheduledExecutorService

Used for delayed and periodic tasks.

```java
ScheduledExecutorService scheduler =
        Executors.newScheduledThreadPool(2);

scheduler.schedule(() -> doOnce(), 1, TimeUnit.SECONDS);
```

Periodic execution

a) scheduleAtFixedRate

```java
scheduler.scheduleAtFixedRate(
    () -> doRepeated(),
    0, 5, TimeUnit.SECONDS
);
```

Runs first task after initial delay (0 sec here)

Subsequent tasks attempt to run at exact intervals (every 5 sec)

If a task takes longer than the period → next task starts immediately after previous finishes

Good for fixed-rate tasks like heartbeats or monitoring

b) scheduleWithFixedDelay

```java
scheduler.scheduleWithFixedDelay(
    () -> doRepeated(),
    0, 5, TimeUnit.SECONDS
);
```

Runs first task after initial delay

Waits 5 sec after task finishes before starting the next

Useful when task execution time is variable and you want consistent delay between runs

scheduleWithFixedDelay was introduced to avoid overlapping executions by ensuring each run starts only after the previous finishes plus a fixed delay.


## ThreadPoolExecutor

Core class for Java thread pools

Implements ExecutorService

Provides fine-grained control over:

Thread creation

Task queueing

Thread lifetime

Task rejection policies

All standard executors (newFixedThreadPool, newCachedThreadPool, newScheduledThreadPool) are built on top of ThreadPoolExecutor internally


```java
new ThreadPoolExecutor(
    corePoolSize,             // minimum threads to keep alive
    maximumPoolSize,          // max threads
    keepAliveTime,            // idle time for extra threads
    TimeUnit unit,            // unit for keepAliveTime
    BlockingQueue<Runnable>,  // work queue
    ThreadFactory,            // thread creation
    RejectedExecutionHandler  // policy when queue full
);

```

## Standard executors

+------------------------+---------+----------------------------+
| Executor Type           | Threads | Notes                      |
+------------------------+---------+----------------------------+
| newFixedThreadPool(n)   | n       | Always n threads, tasks queued |
| newCachedThreadPool()   | 0→∞     | Threads created on demand, idle die |
| newSingleThreadExecutor()| 1      | Single thread, tasks sequential |
| newScheduledThreadPool(n)| n      | Delayed/periodic tasks, n threads |
| newSingleThreadScheduled | 1      | Single thread, scheduled tasks sequential |
+------------------------+---------+----------------------------+

## ThreadPoolExecutor better for production

``` java
ExecutorService pool = new ThreadPoolExecutor(
    4,
    8,
    60,
    TimeUnit.SECONDS,
    new ArrayBlockingQueue<>(100),
    Executors.defaultThreadFactory(),
    new ThreadPoolExecutor.CallerRunsPolicy()
);

```
Custom ThreadPoolExecutor is better for production because it lets you:

Control threads – core and max threads for predictable concurrency.

Limit queue size – prevent memory spikes from too many tasks.

Handle overload – choose a rejection policy (e.g., CallerRuns).

Name threads / set priority – easier debugging and monitoring.

Tune scaling / keep-alive – optimize for task patterns.

Standard Executors are convenient, but custom executors give full control and safer, predictable behavior.

## ForkJoinPool & ForkJoinTask

ForkJoinPool: a special thread pool for divide-and-conquer tasks; uses work-stealing for load balancing.

ForkJoinPool + ForkJoinTask splits work recursively, forks subtasks to run in parallel, computes one in the current thread, joins others, and combines results efficiently.

ForkJoinTask: base class for tasks run in ForkJoinPool

RecursiveTask<V> → returns a result

RecursiveAction → no result

Workflow:

Task’s compute() splits large work into subtasks recursively.

fork() schedules a subtask asynchronously in the pool.

Current thread can compute another subtask synchronously.

join() waits for the forked subtask and retrieves the result.

Combine results from subtasks → final result.

Works efficiently without creating extra threads; threads execute subtasks and steal work when idle.

```java
class SumTask extends RecursiveTask<Integer> {
    private final int[] arr;
    private final int start, end;

    protected Integer compute() {
        if (end - start <= 10) {
            int sum = 0;
            for (int i = start; i < end; i++) sum += arr[i];
            return sum;
        }
        int mid = (start + end) / 2;
        SumTask left = new SumTask(arr, start, mid);
        SumTask right = new SumTask(arr, mid, end);
        left.fork();
        return right.compute() + left.join();
    }
}


// Submit to ForkJoinPool
ForkJoinPool pool = new ForkJoinPool();
int result = pool.invoke(new SumTask(data, 0, data.length));
```
