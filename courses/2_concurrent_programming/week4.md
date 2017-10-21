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

This approach doesn't suffer from liveness issues, i.e. it's not possible for a thread to spin forever in the while loop without making any progress.

# Concurrent queue

# Linearizability

# Concurrent hash map

# Concurrent minimum spanning tree algorithm
