RDD stands for Resilient Distributed Dataset, it's an immutable, partitioned collection of elements that can be operated on in parallel.

Operations like `map`, `filter`, and `persist` apply to all RDD in spark. 

Internally, each [RDD](https://github.com/apache/spark/blob/master/core/src/main/scala/org/apache/spark/rdd/RDD.scala) is characterized by five main properties:
 
- A list of partitions
- A function for computing each split
- A list of dependencies on other RDDs
- Optionally, a Partitioner for key-value RDDs (e.g. to say that the RDD is hash-partitioned)
- Optionally, a list of preferred locations to compute each split on (e.g. block locations for an HDFS file)

# Operations

## `map`

## `persist`

By default, RDD is persisted into memory

```scala
def persist(): this.type = persist(StorageLevel.MEMORY_ONLY)
def cache(): this.type = persist()
``` 