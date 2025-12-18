# Concurrent Collections

# ConcurrentHashMap - Java

- Thread-safe, high-performance `Map` for concurrent access (`java.util.concurrent`).
- Allows **concurrent reads and writes**; `get()` is **non-blocking**, writes use **per-bucket locks**.
- Supports **atomic operations**: `putIfAbsent`, `compute`, `merge`, `replace`.
- Iterators are **weakly consistent**, safe during concurrent updates.
- Does **not allow null keys or values**.
- Best for **shared caches, counters, session storage** where many threads read/write.

## Quick Example

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("A", 1);                  // thread-safe write
map.putIfAbsent("B", 2);          // atomic insert
map.compute("A", (k,v) -> v+10);  // atomic update

System.out.println(map.get("A")); // thread-safe, non-blocking read

for (String key : map.keySet()) { // weakly consistent iterator
    System.out.println(key + " -> " + map.get(key));
}
```

# CopyOnWriteArrayList / CopyOnWriteArraySet - Java

- Thread-safe collections optimized for **read-heavy workloads**.
- **Reads** (`get`, iteration) are **lock-free**; iterators are **snapshot-based**.
- **Writes** (`add`, `remove`, `set`) are **synchronized internally**, create a **new copy** of the array, and are **serialized**.
- Best for **many readers, few writers**; avoids `ConcurrentModificationException`.

## Quick Example

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
list.add("A"); // write is synchronized internally
list.add("B");

for (String s : list) {
    System.out.println(s); // snapshot iterator, safe during concurrent writes
}

CopyOnWriteArraySet<String> set = new CopyOnWriteArraySet<>();
set.add("X");
set.add("Y");
set.add("X"); // ignored, no duplicates
