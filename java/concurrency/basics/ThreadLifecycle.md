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
    +---------+  +---------------+  +----------------+
    | Waiting |  | Timed Waiting |  |   Blocked      |
    +---------+  +---------------+  +----------------+
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

Thread has finished execution of run() after being started.

run() method completes

Thread cannot be restarted (start() called again → IllegalThreadStateException)

Terminated threads cannot be restarted. You need a new Thread object to run again.

