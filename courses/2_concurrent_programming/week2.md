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

A critical section ensures *mutual exclusive* access to resources. In the example above, the `balance` resource is access to at most one thread at a time.
