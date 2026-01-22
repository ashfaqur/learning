# Collections Framework

- [Collections Framework](#collections-framework)
- [List](#list)
- [Set](#set)
- [Map](#map)
- [ArrayList](#arraylist)
- [LinkedList](#linkedlist)
- [HashSet](#hashset)
- [TreeSet](#treeset)
- [HashMap](#hashmap)
- [LinkedHashMap](#linkedhashmap)
- [TreeMap](#treemap)
- [PriorityQueue](#priorityqueue)
- [Stack](#stack)
- [Deque / ArrayDeque](#deque--arraydeque)


# List
- Ordered collection
- Allows duplicates
- Index-based access
- Use when order and position matter

# Set
- Collection of unique elements
- No duplicates
- No index access
- Use when uniqueness is required

# Map
- Key-value pairs
- Keys are unique, values can duplicate
- Access by key
- Not part of Collection interface


# ArrayList
- Backed by dynamic array
- O(1) random access
- Slow insert/delete in middle
- Lower memory usage
- Best for read-heavy workloads
  
ArrayList is usually better
- Faster random access
- Better iteration performance
- Lower memory overhead

# LinkedList
- Doubly linked list
- O(n) access
- Fast insert/delete if node known
- Higher memory usage
- Good for queues and frequent modifications

LinkedList is better only in specific cases
- Used as Queue or Deque (add/remove at ends)
- Frequent removals during iteration
- Frequent insert/remove at beginning


# HashSet
- Unordered set
- Backed by HashMap
- O(1) average operations
- Allows one null
- Best for fast uniqueness checks

# TreeSet
- Sorted set
- Backed by TreeMap (Red-Black Tree)
- O(log n) operations
- No null elements
- Best when sorted order or range queries are needed

# HashMap
- No ordering
- O(1) average operations
- Allows one null key
- Best for fast lookups

# LinkedHashMap
- Maintains insertion or access order
- Slightly slower than HashMap
- Useful for LRU caches

# TreeMap
- Sorted by key
- O(log n) operations
- No null keys
- Best for ordered traversal and range queries

# PriorityQueue
- Implemented as binary heap (array-based)
- Default is min-heap
- add / poll: O(log n)
- peek: O(1)
- Not FIFO, not fully sorted
- Iteration order is undefined
- Best for repeated access to min or max element

# Stack
- Legacy class (extends Vector)
- LIFO behavior
- Methods: push, pop, peek, empty
- Synchronized (slower)
- Use-cases: DFS, undo, expression evaluation
- Modern code prefers ArrayDeque

# Deque / ArrayDeque
- Double-ended queue
- Can act as stack or queue
- Fast O(1) add/remove at both ends
- Does not allow nulls
- Use-cases: stack replacement, BFS/DFS, sliding window
- ArrayDeque is preferred over LinkedList for performance

ArrayDeque
- Backed by circular array, not linked list
- Head and tail pointers move around the array
- Add/remove at both ends: O(1) amortized
- Resizes internally when full
- Null elements not allowed
- Faster than LinkedList due to better cache locality


