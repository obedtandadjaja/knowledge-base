# Apache Spark

Spark was introduced to speed up Hadoop computational computing software process. Against a common belief, Spark is not a modified version of Hadoop. It is not dependent on Hadoop because it has its own cluster management; Hadoop is just one of the ways to implement Spark. Spark uses Hadoop for storage purpose only.

Spark extends MapReduce model which includes interactive queries and stream processing. The main feature of Spark is its in-memory cluster computing that increases the processing speed of an application.

## Spark on Hadoop

![picture](https://www.tutorialspoint.com/apache_spark/images/spark_built_on_hadoop.jpg)

There are 3 ways of Spark deployment on Hadoop.

## Components of Spark

![picture](https://www.tutorialspoint.com/apache_spark/images/components_of_spark.jpg)

### Apache Spark Core

The underlying general execution engine for spark platform that all other functionality is built upon. It provides in-memory computing and referencing datasets in external storage systems

### Spark SQL

Provides data abstraction - SchemaRDD - to support structured or semi-structured data

### Spark Streaming

Performs streaming analytics. Ingests data in mini-batches and performs RDD (Resilient Distributed Datasets) transformations on those mini-batches of data

### MLlib (Machine Learning Library)

Machine learning framework above Spark

### GraphX

Distributed graph-processing framework on top of Spark. Provides an API for expressing graph computation that can model user-defined graphs using Pregel abstraction API

## Resilient Distributed Datasets - RDD

RDD is a fundamental data structure of Spark. It is an immutable distributed collection of objects. Each dataset is divided into logical partitions, which may be computed on different nodes of the cluster. RDDs can contain any user-defined classes.

RDD is:
- Read-only
- Partitioned
- Can be created through deterministic operations on stable storage or other RDDs
- Fault-tolerant
- Can be operated on in parallel

2 ways to create RDDs:
1. Parellelizing an existing collection in driver program
2. Referencing a dataset in external storage system

Spark uses RDD to achieve faster and efficient MapReduce operations. If memory ran out, then it will store results on disk

### Why MapReduce is slow

MapReduce is used to process large datasets in parallel, distributed algorithm on a cluster. It allows user to not worry about work distribution and fault tolerance.

The only way to reuse data between computations (2 MapReduce jobs) is to write it to disk instead of keeping it in memory to be used in the next job. Data sharing is slow in MapReduce due to `replication, serialization, and Disk IO`. Most Hadoop applications spend more than 90% of the time doing HDFS read-write operations.

## Spark Shell

Spark provides a shell available in Scala and Python.

```
$ spark-shell
```

## RDD Transformations

RDD transformations returns pointer to new RDD and allows you to create dependencies between RDDs. Each dependency chain has a function for calculating its data and a pointer to its parent RDD. Spark will not execute unless you call some transformation or action that will trigger job creation and execution.

- `map(func)`
- `filter(func)` - returns a new dataset by selecting elements on which func returns true
- `flatMap(func)`
- `mapPartitions(func)` - similar to map, but runs separately on each partition of the RDD, so func must be of type Iterator
- `mapPartitionsWithIndex(func)` - similar to mapPartitions but also provides func with an integer value representing the index of the partition
- `sample(withReplace, fraction, seed)` - sample a fraction of data
- `union(otherDataset)` - returns a new dataset that is the union of datasets
- `intersection(otherDataset)` - returns a new dataset that is the intersaction of datasets
- `distinct([numTasks])`
- `groupKey([numTasks])` - when called on a dataset of (K, V) pairs, returns a dataset of (K, Iterable<V>) pairs. Note: If you are doing aggregation over each key, using reduceByKey or aggregateByKey will yield much better performance
- `reduceByKey(func, [numTasks])` - reduced by func
- `aggregateByKey(zeroValue)(seqOp, comOp, [numTasks])` - when called on a dataset of (K, V) pairs, returns a dataset of (K, U) pairs where the values for each key are aggregated using the given combine functions and a neutral "zero"value
- `sortByKey([ascending], [numTasks])`
- `join(otherDataset, [numTasks])` - when called on a dataset of type (K, V) and (K, W) returns a dataset of (K, (V, W)) pairs. Other joins are supported through `leftOuterJoin, rightOuterJoin, and fullOuterJoin`
- `cartesian(otherDataset)` - when called on datasets of types T and U, returns a dataset of (T, U) pairs
- `pipe(command, [envVars])` - pipe each partition of the RDD through a shell command
- `coalesce(numPartitions)` - decrease the number of partitions in the RDD to numPartitions
- `repartition(numPartitions)` - reshuffle the data in RDD randomly to create either more or fewer partitions and balance it across them
- `repartitionAndSortWithinPartitions(partitioner)` - reparition the RDD according to the given partitioner and, within each resulting partition, sort records by their keys

## Actions

Actions return values, RDD transformations return a new RDD

- `reduce(func)`
- `collect()` - returns all the elements of the dataset as an array at the driver program
- `count()`
- `first()`
- `take(n)`
- `takeSample(withReplacement, num, [seed])`
- `takeOrdered(n, [ordering])`
- `saveAsTextFile(path)` - writes the elements as a text file in a given directory. Spark calls toString on each element
- `saveAsSequenceFile(path)` - writes the elements of dataset as Hadoop SequenceFile
- `saveAsObjectFile(path)` - writes the elements of dataset in a simple format using Java serialization, which can then be loaded using `SparkContext.objectFile()`
- `countByKey()`
- `foreach(func)`
