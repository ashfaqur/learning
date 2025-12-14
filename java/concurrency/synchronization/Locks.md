# Locks

- [Locks](#locks)
  - [ReentrantLock](#reentrantlock)
    - [Try Lock](#try-lock)
    - [Fairness](#fairness)
  - [ReadWriteLock](#readwritelock)
    - [Pitfalls](#pitfalls)
      - [Fair ReadWriteLock](#fair-readwritelock)
  - [StampedLock](#stampedlock)
    - [Write lock (exclusive)](#write-lock-exclusive)
    - [Read lock (shared)](#read-lock-shared)
    - [Optimistic read (non-blocking)](#optimistic-read-non-blocking)


## ReentrantLock

A mutual exclusion lock, similar to synchronized, but with more features.

“Reentrant” means:

A thread holding the lock can acquire it again without deadlocking.

Always unlock in finally.

```java
import java.util.concurrent.locks.ReentrantLock;

class Counter {
    private final ReentrantLock lock = new ReentrantLock();
    private int count;

    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```

`lock` is blocking

### Try Lock

``` java
if (lock.tryLock()) {
    try {
        // got the lock
    } finally {
        lock.unlock();
    }
}
```
If tryLock() does not get the lock, it returns false immediately and the code inside the if block is skipped.

So better to use:

```java
lock.tryLock(1, TimeUnit.SECONDS);
```

OR

```java
lock.lockInterruptibly();
```
### Fairness

```java
ReentrantLock lock = new ReentrantLock(true);
```

Fair lock → threads acquire in FIFO order

Slower but avoids starvation

Fair locks are slower because they prevent lock barging, increase context switches, reduce cache locality, and add queue management overhead to enforce FIFO ordering.

## ReadWriteLock

Separates read locks and write locks.

Multiple readers can hold the lock simultaneously

Writers get exclusive access

Best when:

Reads >> Writes

```java
import java.util.concurrent.locks.*;

class Cache {
    private final ReadWriteLock rwLock = new ReentrantReadWriteLock();
    private int data;

    public int read() {
        rwLock.readLock().lock();
        try {
            return data;
        } finally {
            rwLock.readLock().unlock();
        }
    }

    public void write(int value) {
        rwLock.writeLock().lock();
        try {
            data = value;
        } finally {
            rwLock.writeLock().unlock();
        }
    }
}

```

`readLock` Multiple readers at the same time

`writeLock` Only one writer, exclusive

`Reader + Writer` No readers while writer active

Key points

Readers do not block each other

Writers block all readers and writers

Write lock is reentrant


### Pitfalls

Writer starvation if many readers: 
A writer thread may wait indefinitely because new reader threads keep acquiring the read lock, preventing the writer from ever getting the write lock.

#### Fair ReadWriteLock

```java
ReadWriteLock lock = new ReentrantReadWriteLock(true);
```
Fair mode guarantees:

Threads acquire locks in FIFO order

Once a writer is waiting, new readers are blocked

📉 Trade-off: slower throughput


More complex than synchronized

Lock upgrading (read → write) can deadlock

When to use ReadWriteLock

✅ Heavy read, rare write workloads

❌ Balanced read/write → ReentrantLock

## StampedLock

StampedLock is a Java 8 concurrency utility that supports three kinds of locks:

Read lock → shared access (like ReadWriteLock.readLock())

Write lock → exclusive access (like ReadWriteLock.writeLock())

Optimistic read lock → non-blocking read with validation

Key features:

Very fast for reads

Writer-biased → avoids writer starvation better than non-fair ReadWriteLock

Returns a stamp (long) to identify the lock version, used for unlocking

StampedLock is not reentrant — the same thread cannot acquire the same lock multiple times.

### Write lock (exclusive)

```java
StampedLock lock = new StampedLock();

long stamp = lock.writeLock();  // blocks until acquired
try {
    data = value;               // write
} finally {
    lock.unlockWrite(stamp);
}
```

Only one thread can hold it

Blocks readers until released

### Read lock (shared)

```java
long stamp = lock.readLock();  // blocks if a writer holds the lock
try {
    int result = data;         // read
} finally {
    lock.unlockRead(stamp);
}
```

Multiple readers can run concurrently

Writers block readers and vice versa

### Optimistic read (non-blocking)

```java
long stamp = lock.tryOptimisticRead();
int result = data;  // read data

if (!lock.validate(stamp)) {
    stamp = lock.readLock();  // fallback to proper read lock
    try {
        result = data;
    } finally {
        lock.unlockRead(stamp);
    }
}

```

Does not block writers

After reading, you validate to check if a writer modified the data during the read

If modified → fallback to normal read lock

Super efficient for mostly-read, rarely-write cases.
