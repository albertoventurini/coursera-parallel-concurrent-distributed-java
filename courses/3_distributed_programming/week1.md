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

# Spark Framework

# TF-IDF Example

# Page Rank Example

