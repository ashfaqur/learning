# Thread Life Cycle

            +---------+
            |  New    |
            +---------+
                |
            start()
                v
            +---------+
            | Runnable|
            +---------+
                |
        Scheduler picks CPU
                v
            +---------+
            | Running |
            +---------+
            /    |    \
    wait()/sleep()/join() ...
        v       v       v
    +---------+  +---------------+
    | Waiting |  | Timed Waiting |
    +---------+  +---------------+
        ^           |
        | notify()/ timeout
        +-----------+
                |
            +---------+
            |Terminated|
            +---------+


Java Thread Lifecycle

A Java thread can be in one of six states:

## New (Created)

Thread object has been created but not started yet.

    Thread t = new Thread(() -> System.out.println("Hello"));

## Runnable (Ready to run)

Thread is ready to run and waiting for CPU time.

    t.start();
    
## Running (Executing)

Thread currently running.

Only one thread per CPU core can actually run at a time.

## Waiting (Indefinite wait)

Thread is waiting forever for another thread to notify it.

Methods that put a thread here:

Object.wait() (without timeout)

Thread.join() (without timeout)

LockSupport.park()

Transition out:

Another thread calls notify() / notifyAll() (or unblocks the lock)

Waiting threads do not consume CPU, they are suspended until notified.

When a thread calls wait() on an object, it releases the monitor (lock) on that object immediately and goes into the WAITING state.

## Timed Waiting

Thread waits for a limited time.

Timeout occurs → back to Runnable

Notified early → back to Runnable

## Terminated (Dead)



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

    public static void main(String[] args) throws InterruptedException {
        WaitExample example = new WaitExample();

        Thread t1 = new Thread(example::doWait, "Thread-1");
        Thread t2 = new Thread(() -> {
            try {
                Thread.sleep(2000); // Give t1 time to enter WAITING
                example.doNotify();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "Thread-2");

        t1.start();
        t2.start();

        t1.join();
        t2.join();
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
