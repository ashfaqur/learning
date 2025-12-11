# Concurrency

## Basics

Creating Threads

### Extending the Thread class

``` java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println(
            "Thread running: " + Thread.currentThread().getName());
    }
}

MyThread thread = new MyThread();
thread.start();

```

Limitation: Cannot extend another class because Java supports only single inheritance.

### Implementing Runnable Interface

``` java
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println(
            "Runnable running: " + Thread.currentThread().getName());
    }
}

Thread t1 = new Thread(new MyRunnable());
t1.start();

// With lamda

Thread thread = new Thread(() -> {
    System.out.println(
            "Runnable running: " + Thread.currentThread().getName());
});

```

More flexible than extending Thread because class can still extend another class.

Separates task from thread control.

Allows multiple threads to share the same Runnable instance.


### Using Callable and Future

Callable is similar to Runnable but can return a value and throw checked exceptions.

```java

class MyCallable implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        return 42;
    }
}

Callable<Integer> callable = new MyCallable();
FutureTask<Integer> futureTask = new FutureTask<>(callable);
Thread thread = new Thread(futureTask);
t.start();

// Wait for result
Integer result = futureTask.get();
System.out.println("Result from Callable: " + result);

```

Using ExecutorService

```java
 Callable<String> task = () -> {
    if (Math.random() < 0.5) {
        throw new IOException("Network failed!");
    }
    return "Success!";
};

ExecutorService executor = Executors.newSingleThreadExecutor();
Future<String> future = executor.submit(task);
try {
    String result = future.get();
    System.out.println("Result: " + result);

} catch (ExecutionException e) {
    // Checked exceptions are wrapped inside ExecutionException
    Throwable cause = e.getCause();
    System.out.println("Task failed with: " + cause.getClass().getSimpleName());
    System.out.println("Message: " + cause.getMessage());
} catch (InterruptedException e) {
    System.out.println("Interrupted while waiting");
} finally {
    executor.shutdown();
}

```
