# Singletons


## Singleton Eager

```java
public class SingletonEager {

    private static final SingletonEager INSTANCE = new SingletonEager();

    private SingletonEager() {}

    public static SingletonEager getInstance() {
        return INSTANCE;
    }
}
```

`static`

The INSTANCE belongs to the class, not to any object.

Created when the class is loaded by the JVM.

Class loading is synchronized internally by the JVM, so this is thread-safe.

`final`

The reference INSTANCE cannot be reassigned after initialization.

Guarantees that no other instance can replace it later.

`getInstance()`

Provides global access point to the single instance.


## Singleton Not Safe

``` java
public class SingletonLazyNonThreadSafe {

    private static SingletonLazyNonThreadSafe instance;

    private SingletonLazyNonThreadSafe() {}

    public static SingletonLazyNonThreadSafe getInstance() {
        if (instance == null) {
            instance = new SingletonLazyNonThreadSafe();
        }
        return instance;
    }
}
```

## Singleton Safe With Synchronized

``` java
public class SingletonLazyNonThreadSafe {

    private static SingletonLazyNonThreadSafe instance;

    private SingletonLazyNonThreadSafe() {}

    public static synchronized SingletonLazyNonThreadSafe getInstance() {
        if (instance == null) {
            instance = new SingletonLazyNonThreadSafe();
        }
        return instance;
    }
}
```
## Singleton With Double Checking

``` java
public class SingletonDoubleCheckLocking {

    private static volatile SingletonDoubleCheckLocking instance;

    private SingletonDoubleCheckLocking() {}

    public static SingletonDoubleCheckLocking getInstance() {
        if (instance == null) {
            synchronized (SingletonDoubleCheckLocking.class) {
                if (instance == null) {
                    instance = new SingletonDoubleCheckLocking();
                }
            }
        }
        return instance;
    }
}
```
`volatile`

Ensures visibility across threads: changes to instance by one thread are visible to all threads immediately.

Prevents the JVM from “optimizing” by reordering the steps of object creation, ensuring full construction before reference is visible.

`synchronized (SingletonDoubleCheckLocking.class)`

Ensures only one thread can create the instance.

## Singleton Lazy Holder

``` java
public class SingletonLazyHolder {

    private static class LazyHolder {
        private static final SingletonLazyHolder INSTANCE = new SingletonLazyHolder();
    }

    private SingletonLazyHolder() {}

    public static SingletonLazyHolder getInstance() {
        return LazyHolder.INSTANCE;
    }
}
```
`LazyHolder` is a static nested class.

The first time `getInstance()` is called, JVM loads `LazyHolder`.

JVM guarantees that class initialization is thread-safe.

## Singleton Enum

``` java
public enum SingletonEnum {
    INSTANCE;
}
```
Java ensures that enums are inherently singletons.

The `INSTANCE` is created by the JVM when the enum class is loaded.

JVM guarantees:

Only one instance per enum constant.

Thread safety.

Protection against serialization and reflection attacks.
