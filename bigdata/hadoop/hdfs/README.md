# Proto


HDFS is using [Google ProtoBuf library](https://developers.google.com/protocol-buffers/) to generate the RPC code.

# Process

## Interface

Since hadoop is umbrella project for Yarn and HDFS, all common interfaces are put into [hadoop-common-project](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project) folder even they're HDFS specific. I guess it's mainly for communication purpose that any other projects can use these interfaces directly.

* [FileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/fs/FileSystem.java) defines the interface that server provides.
* [DistributedFileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DistributedFileSystem.java) is the implementation of [FileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/fs/FileSystem.java).

## Call stack under the hood

[Process on create a new file in HDFS](./Create_File.md)
[Lease](./Lease.md)
[Encryption](./Encryption.md)

# Code Analysis

## Name Node

## Data Node

### Block Transfer


* [FileIoProvider](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/datanode/FileIoProvider.java)

|Method             |Description    |
|-------------------|---------------|
|||

* [BlockReceiver](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/datanode/BlockReceiver.java) - 

* [LocalReplica](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/datanode/LocalReplica.java)
 
