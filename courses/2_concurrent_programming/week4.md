# Optimistic concurrency

Optimistic concurrency is a pattern that uses a loop to ensure that an operation completed successfully.

For example, consider the *AtomicInteger* class. In this class we might have a `getAndAdd(d)` method which adds a *d* delta to the current value and return the original value. This is obviously non thread-safe, so we can use optimistic concurrency to eliminate the data race.

In the following example, we want to implement the `getAndAdd(d)` method using optimistic concurrency. We suppose that the AtomicInteger class already contains a `compareAndSet` method that operates atomically (e.g. by using an atomic instruction on the CPU). We use that method to implement `getAndAdd` with optimistic concurrency.

In pseudocode:

```
AtomicInteger {

  bool compareAndSet(cur, next) {
    // Is the current value == cur?
    // If yes, then set the current value to next and return true.
    // If not, then return false.
    // This is an atomic operation.
  }
  
  int getAndAdd(delta) {
    while(true) {
      cur = this.get();
      next = cur + delta;
      if(this.compareAndSet(cur, next))
        return cur;
    }
  }
}
```

This approach doesn't suffer from liveness issues:

* no deadlocks because it doesn't block on anything
* no livelocks because it's not possible for a thread to spin forever in the while loop without making any progress.

This approach can have better performance than locks, isolations or actors if the contention on a shared object is not high.

# Concurrent queue

This lecture provides a basic sketch of how optimistic concurrency might be used to implement a concurrent queue. The pattern is similar to the AtomicInteger class above. In this case, the `compareAndSet` operation is performed on `AtomicReference` objects such as `head` and `tail`.

Every operation that reads or writes to a pointer must be protected from data races. It is possible to do that with optimistic concurrency but it is not as trivial as using locks, isolation or actors.

# Linearizability

Linearizability is a correctness property for concurrent data structures. Given n threads operating on a concurrent data structure in parallel, what are the permissible end statuses? Linearizability answers this question.

For example, suppose you have a concurrent queue that supports operations `enqueue()` and `dequeue()`. You have threads T1 and T2 operating on the same concurrent queue. Thread T1 calls `enqueue(x)` and thread T2 calls `enqueue(y)`. What are the possible outcomes of calling `dequeue()`?

Both x and y are valid results. The answer depends on which enqueue operation completed first, but in either case the history of operations is linearizable (i.e. the sequence of operations leads to a valid outcome). Therefore the concurrent queue is linearizable.

# Concurrent hash map

The `ConcurrentHashMap` class implements a thread-safe version of `HashMap`. It supports the usual Map operations such as `get()` and `put()`. Additionally, it provides specific operations such as `putIfAbsent()`.

Operations such as `get()`, `put()` and `putIfAbsent()` are linearizable, while other aggregate operations such as `clear()` and `putAll()` are not linearizable.

# Concurrent minimum spanning tree algorithm
