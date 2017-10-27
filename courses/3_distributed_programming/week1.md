# Introduction to Map-Reduce

This lecture is a brief introduction to the map-reduce framework. This framework is normally used to enable "big data" computations in parallel.

The map-reduce framework is composed of:

* an input consisting of a collection of key-value pairs
* a *map* function which is applied to each key-value pair
* a *grouping* stage which groups together intermediate keys with the same value
* a *reduce* function which transforms the intermediate key-value pairs in the final output.

### Mapping

The input to a map-reduce computation is a collection of key-value pairs. Most data can be thought of as a collection of key-value pairs, e.g. files can be represented as key = fileName, value = fileContent.

For each key-value pair *(kA, vA)*, the framework applies a *map* function that produces an intermediate result:

```
(kA, vA) --> map function --> [ (kA1, vA1), (kA2, vA2), ... (kAn, vAn) ]
```

Note: the intermediate keys need not be in the same space as the initial key. For example, the initial keys might be file name and the intermediate keys might be words.

### Grouping

The grouping stage collapses all the pairs that have the same key. E.g. if *kA1* == *kA2* == *k*, then

```
[ (kA1, vA1), (kA2, vA2), ... (kAn, vAn) ] --> grouping --> [ (k, [vA1, vA2]), ... ]
```

The grouping stage produces (k, v) pairs where *v* is the list of values that share the same key.

### Reduce

The framework applies the *reduce* function to intermediate values with the same key. E.g.:

```
[ (k, [vA1, vA2]), ... ] --> reduce function --> [ (k, finalValueA), ... ]
```

## Word count

A typical example that is used to illustrate map-reduce is the following task: given a set of files, extract the frequency of words of all files.

The input data is a collection of files that can be represented as a key-value pair: (fileName, fileContent).

For each key-value pair, the *map* function scans the file content and emits a collection of intermediate key-value pairs with all values set to 1:

```
(word1, 1), (word2, 1), (word3, 1), (word2, 1)
```

Note that words may be repeated. In the example above, *word2* appears twice.

The grouping stage puts together intermediate results with the same key:

```
(word1, [1]), (word2, [1, 1]), (word3, [1])
```

Finally, the *reduce* function in this case is just a sum and produces the following output:

```
(word1, 1), (word2, 2), (word3, 1)
```

# Hadoop Framework

*Hadoop* is an open-source implementation of the map-reduce paradigm developed by Apache. The key points of Hadoop are:
* it allows developers to specify map and reduce functions as Java programs
* it allows for higher-level query languages such as Hive or Pig
* it supports fault-tolerance, i.e. if a node (server) fails its map or reduce task, the task will be executed again (this is possible because the map and reduce functions are pure functions).

# Spark Framework

*Apache Spark* is similar to, but more general than, Hadoop.

## In-memory computations

One feature of Spark that sets it apart from Hadoop is its ability to perform in-memory computations. By contrast, Hadoop uses the disk to load and save intermediate computation results. This makes Spark significantly faster than Hadoop, but it also restricts the amount of data that can be processed on each server to the amount of memory available on the server.

## Input model

Spark doesn't restrict input data to be in the form of key-value pairs, but allows for a more generic representation of data based on *Resilient Distributed Dataset* (RDD).

An RDD is a distributed collection on which we can perform transformations and actions. There are two ways to create an RDD:
* by parallelizing an existing collection in a Spark program;
* by reading it from storage (e.g. from HDFS)

RDDs are *resilient* because they support node failures transparently.

RDDs are more generic than key-value pairs because they are generic collections of objects. I.e. you can put Java, Scala or Python objects into an RDD.

More information on RDDs can be found on the (Spark documentation)[https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#resilient-distributed-datasets-rdds]

## Data manipulation

Spark provides various APIs for manipulating RDDs. These APIs are classified as:
* Transformations (map, filter, join, ...)
* Actions (reduce, join, collect, ...)

# TF-IDF Example

# Page Rank Example

