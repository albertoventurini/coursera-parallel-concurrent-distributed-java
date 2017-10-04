# Critical sections

A *critical section* is a part of a program that is protected from potential data races. A critical section ensures that multiple threads can access shared resources safely. This is ensured by enforcing access to shared resources to one thread at a time.

Conceptually, a critical section is an *isolated* block.

The usual example is bank accounts. If you model a bank account as a mutable object, and you have multiple threads operating on the same bank account, you want to ensure that concurrent operations don't generate data races. E.g. in pseudocode:

```
class Account {
  private int balance;
  //...
  public void deposit(int amount) {
    isolated { // Critical section
      balance += amount;
    }
  }
}
```

A critical section ensures *atomicity*. In the example above, the two operations of reading the current amount of `balance` and writing back the updated amount are conflated into an atomic operation; that is, both operations (read and write) must be finished before another thread can access the critical section. As far as other threads are concerned, they are effectively one, single, atomic operation.

A critical section ensures *mutual exclusive* access to resources. In the example above, the `balance` resource is accessed to at most one thread at a time.

# Object-based isolation

Object-based isolation is a generalization of the `isolated` construct which was introduced in the previous section. Object-based isolation allows you to specify a list of objects whose access must be mutually exclusive.

- If two threads try to access object-based isolation sections having an empty intersection of objects, they can proceed in parallel;
- if two threads try to access object-based isolation sections having a non-empty intersection of objects, they can't proceed in parallel.

For example, suppose you're developing a thread-safe doubly linked list. When you're deleting a node you'll be modifying the pointers of the previous and next node:

```
void delete(Node n) {
  n.next.prev = n.prev;
  n.prev.next = n.next;
}
```

In order to make it thread-safe, we can use object-based isolation and specify the nodes that require mutual-exclusive access:

```
void delete(Node n) {
  isolated(n, n.prev, n.next) {
    n.next.prev = n.prev;
    n.prev.next = n.next;
  }
}
```

This is better than just using a critical section, because two threads can proceed in parallel if the `delete` operations don't interfere with each other.

E.g. given the following list:

```
N1 - N2 - N3 - N4 - N5 - N6
```

two threads can delete nodes N2 and N5 in parallel.

Object-based isolation can be implemented by using locks, but it's often challenging to do it correctly with locks without incurring in some liveness issues.

## Relationship with monitors

Previously we defined a monitor as a lock that provides wait/notify functionalities. However a monitor is also defined as a class whose methods are executed in mutual exclusion (e.g. all methods are wrapped with an `isolated` block). This is akin to saying that all methods in a monitor provide object-based isolation with a singleton list which contains the monitor itself. For example, monitor `M1` might be:

```
Monitor M1 {

  void doSomething() {
    isolated(M1) { // singleton list
      // ...
    }
  }

}
```

Wikipedia article on monitors: https://en.wikipedia.org/wiki/Monitor_(synchronization)

# Concurrent spanning tree algorithm

# Atomic variables

# Read/write isolation
