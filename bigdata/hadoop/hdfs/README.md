# Proto


HDFS is using [Google ProtoBuf library](https://developers.google.com/protocol-buffers/) to generate the RPC code.

# Process

## Interface

Since hadoop is umbrella project for Yarn and HDFS, all common interfaces are put into [hadoop-common-project](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project) folder even they're HDFS specific. I guess it's mainly for communication purpose that any other projects can use these interfaces directly.

* [FileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/fs/FileSystem.java) defines the interface that server provides.
* [DistributedFileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DistributedFileSystem.java) is the implementation of [FileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/fs/FileSystem.java).

## Call stack under the hood

### Client create a new file.
* [DFSClient](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java) creates a client instance, then make a [create](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java#create) call to create a file in HDFS. 
* create method get mode by given permission or default permission if not given.
* create method call [DFSOutputStream.newStreamForCreate](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSOutputStream.java) method to acquire a [DFSOutputStream](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSOutputStream.java) instance.
* after client acquired the [DFSOutputStream](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSOutputStream.java) instance, it calls [beginFileLease](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java#beginFileLease) to start automatic renewal process. [LeaseRenewer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/client/impl/LeaseRenewer.java) is a daemon thread to check if the renewal is expired. 

### Communication protocol
* [DFSClient](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java) holds a retry policy if 10 times retries are not reached.
 

# Name Node

# Data Node

## Block Transfer

* [FileIoProvider](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/datanode/FileIoProvider.java)

|Method             |Description    |
|-------------------|---------------|
|||

* [BlockReceiver](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/datanode/BlockReceiver.java) - 

* [LocalReplica](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/datanode/LocalReplica.java)
 
