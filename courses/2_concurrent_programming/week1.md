# Threads

[Threads](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html) have been part of the Java language since its inception.

In order to create a new thread, you instantiate a new `Thread` object. In its constructor, you pass a `Runnable`, which represents the action to execute when the thread starts.

For example:
```
Thread t = new Thread(new Runnable() {
    @Override
    public void run() {

    }
});
```

In order to start a thread, use `t.start()`. In order to wait for a thread to finish, use `t.join()`. The `join` operation ensures a *happens-before* relationship between the terminating thread and the thread that it's joining into. This means that if we call `t.join()` on thread `t`, we ensure that all statements in thread `t` will be executed before the `join` call terminates.

All Java programs execute in threads, therefore there's a default thread that gets created whenever a new program runs.

## Deadlock with circular joins

A simple way to create a deadlock is by having two threads waiting on each other. For example, in pseudocode:
```

s1 = ... t2.join();
s2 = ... t1.join();

// Thread creation
t1 = new Thread(s1);
t2 = new Thread(s2);

// Start
t1.start();
t2.start();

```
This creates a circular wait that never ends.

# Structured locks

Locks are used to avoid data-race conditions (as well as to establish *happens-before* relationships). The typical example of data race is  incrementing a counter. In pseudocode:
```
int A;

void incr() {
  A = A + 1;
}

Thread t1 = new Thread(incr);
Thread t2 = new Thread(incr);
```

To solve this, we can use structured locks (A.K.A intrinsic locks). Structured locks in Java are implemented with the `synchronized` keyword:
```
void incr() {
  synchronized(A) {
    A = A + 1;
  }
}
```

Key points to take away:

  - Every object in Java has an intrinsic lock
  - This intrinsic lock is acquired when we use the `synchronized` keyword on the object
  - The lock is implicitly released when the `synchronized` block ends
  - In case of a synchronized method, the thread acquires the implicit lock for the method's object.

[Tutorial](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html) on intrinsic locks.

## Guarded blocks

In some occasions a thread must wait until a certain condition is true, before proceeding with its operations. In Java this is achieved by using `wait` / `nofifyAll` operations. E.g. (in pseudocode):
```
synchronized void consume() {
  while(!elements) {
    wait();
  }
  consumeElement();
}

synchronized void produce() {
  produceElement();
  notifyAll();
}

```

