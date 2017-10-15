# Actors

The *actor model* is a concurrency model where an application is made up of discrete entities called *Actors*.

An actor has three components:
* a mailbox
* local, private state
* a set of methods

The main benefit of actors is that the state can only be accessed by the actor owning it. This is in contrast with traditional object-oriented programming, where the state is class-private but not thread-private (meaning that multiple threads share the same state), thus access to the shared state must be protected with mechanisms such as locks and isolated sections.

The only way to modify an object in a pure actor model is by sending messages to the actor that owns that object as part of its local state. Therefore, there's no need for locks and isolated sections when working with actors.

Actors interact with each other by passing messages. Upon receiving a message, an actor can:
* change its local state
* create more actors
* send messages

Messages from different actors can be arbitrarily reordered (there is no ordering guarantee). However, many implementations guarantee order for messages exchanged between the same pair of actors.

Akka's [intro to actors](https://doc.akka.io/docs/akka/2.5.3/java/guide/actors-intro.html)

# Actor examples

## Hello, world

The "hello, world" of actors is an actor that receives string messages and prints them on the console. When a special message is received ("EXIT") the actor shuts down.

Such an actor can be implemented in the following way (in pseudocode):

```
process(s) {
  if(s == "EXIT") {
    exit();
  } else {
    print(s);
  }
}
```

## Pipelines

Actors can be used to implement processing pipelines. For example, we can have two actors in a pipeline chain: the first actor allows only lower-case strings to go through, while the second actor allows only even-length strings. The first actor receives a string from the rest of the world and, if the string is lower-case, it sends it to the second actor. The second actor accepts the string and, if its length is even, it sends it to the rest of the world.
