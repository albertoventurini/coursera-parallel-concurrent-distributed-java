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

  - **Every object in Java has an intrinsic lock**
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

# Unstructured locks

Unstructured locks are explicit lock objects (as opposed to implicit locks). They are unstructured because you don't use the `synchronized` statement in this case.

Some of the benefits of unstructured locks:

- They are more general than structured locks and allow for non-nested pairs of lock/unlock operations, i.e.:
```
lock1.lock();
lock2.lock();
// ...
lock1.unlock();
lock2.unlock();
```
- They provide a `tryLock()` operation, which gives you an option to act in case the lock could not be acquired.
- It's possible to have read/write locks.

Unstructured locks in Java implement the [`Lock`](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/locks/Lock.html) interface. A typical example is [ReentrantLock](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/locks/ReentrantLock.html).

[Tutorial on lock objects](https://docs.oracle.com/javase/tutorial/essential/concurrency/newlocks.html)

# Liveness

Liveness refers to the ability of a concurrent program to make progress, i.e. to not be blocked for some reasons. This is similar to a sequential program not being blocked on an infinite loop.

Some of the factors that prevent liveness:

- deadlocks
- livelocks (e.g. threads interfering with each other and cancelling out each other's progress)
- starvation (e.g. a thread is never scheduled for execution because other threads are "greedy")

[Short tutorial on livelocks and starvation](https://docs.oracle.com/javase/tutorial/essential/concurrency/starvelive.html)

# Dining philosophers

The dining philosophers problem ([wikipedia](https://en.wikipedia.org/wiki/Dining_philosophers_problem)) models *n* philosophers sitting at a round table and alternating between thinking and eating. It's a problem that exposes potential liveness pitfalls. I.e. can we find an eating protocol that ensures that all philosophers get a chance eat?

## Using structured locks

Here's how a philosopher might be modelled with structured locks:

```
while(true) {
  think();
  synchronized(leftFork) {
    synchronized(rightFork) {
      eat();
    }
  }
}
```

This suffers from deadlock. If all philosophers lock on their left forks, no philosopher can lock on their right fork and they'll all be blocked, waiting for their right fork.

## Using unstructured locks

With unstructured locks we can use `tryLock`, thus preventing deadlocks:

```
while(true) {
  if(!tryLock(leftFork)) continue;
  if(!tryLock(rightFork)) {
    unlock(leftFork);
    continue;
  }
  eat();
}
```

However, this opens up the possibility of a livelock, because all threads might lock their left forks, then try to lock their right fork, and in failing to do so they will unlock their left forks and try again.

## Modifying the protocol

We can modify the "eating protocol" (i.e. the algorithm) by saying that the last philosopher must lock the right fork first, whereas all other philosophers can continue locking on their left forks first.

This is akin to saying that all forks are numbered, and the philosophers must first lock on the lower-numbered fork, then lock on the higher-numbered fork.
