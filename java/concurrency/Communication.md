# Inter Thread Communication

- [Inter Thread Communication](#inter-thread-communication)
  - [wait(), notify(), notifyAll()](#wait-notify-notifyall)
    - [wait()](#wait)
    - [notify()](#notify)
    - [notifyAll()](#notifyall)
    - [Classic producer-consumer example](#classic-producer-consumer-example)
    - [Code](#code)
  - [Thread.join()](#threadjoin)
  - [Thread.sleep() vs Thread.yield()](#threadsleep-vs-threadyield)
  - [Lock Conditions (Condition.await(), signal(), signalAll())](#lock-conditions-conditionawait-signal-signalall)


## wait(), notify(), notifyAll()

These are methods of Object, not Thread. They are used to coordinate threads that share a lock.

### wait()

Puts the thread in the Waiting state.

Releases the monitor lock it holds.

Thread stays waiting until notified or interrupted.

```java
synchronized (obj) {
    while (!condition) {
        obj.wait();
    }
    // do work
}
```

### notify()

Wakes up one thread waiting on the object’s monitor.

The awakened thread must reacquire the lock before continuing.

### notifyAll()

Wakes up all threads waiting on the object’s monitor.

Threads compete for the lock.

### Classic producer-consumer example

```java
class DataBox {
    private String data;
    private boolean available = false;

    public synchronized void produce(String value) throws InterruptedException {
        while (available) {
            wait();  // wait until consumed
        }
        data = value;
        available = true;
        notify();  // notify consumer
    }

    public synchronized String consume() throws InterruptedException {
        while (!available){
            wait();  // wait until produced
        }
        available = false;
        notify();  // notify producer
        return data;
    }
}

```

### Code

A simple Java example showing a thread entering the WAITING state using Object.wait(), and another thread notifying it:

```java
class WaitExample {
    private final Object lock = new Object();

    public void doWait() {
        synchronized (lock) {
            try {
                System.out.println(Thread.currentThread().getName() + " is going to wait...");
                lock.wait();  // Thread enters WAITING state here
                System.out.println(Thread.currentThread().getName() + " is resumed!");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public void doNotify() {
        synchronized (lock) {
            System.out.println(Thread.currentThread().getName() + " is notifying...");
            lock.notify(); // Wakes up one waiting thread
        }
    }
}

```
When a thread calls `wait()` on an object, it releases the monitor (lock) on that object immediately and goes into the WAITING state.

`notify()` → wakes up a waiting thread, but the waiting thread must re-acquire the lock before continuing

`notifyAll()` wakes all threads waiting on the monitor.

All these threads move from WAITING → RUNNABLE, meaning they are eligible to run, but they do not start executing immediately.

Synchronized block still enforces mutual exclusion

A synchronized block allows only one thread at a time to execute inside it.

Even after `notifyAll()`, threads must reacquire the lock before they can continue execution in the synchronized block.

So the threads do not execute simultaneously inside the synchronized block.
Java does not guarantee any specific order.

## Thread.join()

Causes the current thread to wait for another thread to finish.

Essentially a higher-level synchronization tool.

```java
Thread t = new Thread(() -> doWork());
t.start();

t.join();  // wait until t finishes
System.out.println("t has finished");
```

## Thread.sleep() vs Thread.yield()

`sleep(ms)`	Suspends current thread for a fixed time. Delay, rate limiting

`yield()`	Suggests scheduler to pause and let other threads run. Hint for fairness, rarely used

Notes:

`sleep()` keeps the lock if outside synchronized block? No, sleep() does not release locks, unlike wait().

`yield()` is only a hint; the scheduler may ignore it.

## Lock Conditions (Condition.await(), signal(), signalAll())

Alternative to wait/notify for ReentrantLock.

Provides multiple condition objects per lock.

More flexible than synchronized + wait/notify.

``` java
class DataBox {
    private String data;
    private boolean available = false;
    private final ReentrantLock lock = new ReentrantLock();
    private final Condition notEmpty = lock.newCondition();
    private final Condition notFull = lock.newCondition();

    public void produce(String value) throws InterruptedException {
        lock.lock();
        try {
            while (available) {
                notFull.await();
            }
            data = value;
            available = true;
            notEmpty.signal();
        } finally {
            lock.unlock();
        }
    }

    public String consume() throws InterruptedException {
        lock.lock();
        try {
            while (!available) {
                notEmpty.await();
            }
            available = false;
            notFull.signal();
            return data;
        } finally {
            lock.unlock();
        }
    }
}


```
