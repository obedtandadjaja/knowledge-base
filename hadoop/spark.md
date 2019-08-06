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

Spark uses RDD to achieve faster and efficient MapReduce operations

### Why MapReduce is slow

