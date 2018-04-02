# Proto


HDFS is using [Google ProtoBuf library](https://developers.google.com/protocol-buffers/) to generate the RPC code.

# Process

## Interface

Since hadoop is umbrella project for Yarn and HDFS, all common interfaces are put into [hadoop-common-project](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project) folder even they're HDFS specific. I guess it's mainly for communication purpose that any other projects can use these interfaces directly.

* [FileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/fs/FileSystem.java) defines the interface that server provides.
* [DistributedFileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DistributedFileSystem.java) is the implementation of [FileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/fs/FileSystem.java).

## Call stack under the hood

### Create a file

#### Client create a new file.

* [DFSClient](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java) creates a client instance, then make a [create](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java#create) call to create a file in HDFS. 
* create method get mode by given permission or default permission if not given.
* create method call [DFSOutputStream.newStreamForCreate](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSOutputStream.java) method to acquire a [DFSOutputStream](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSOutputStream.java) instance.
* after client acquired the [DFSOutputStream](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSOutputStream.java) instance, it calls [beginFileLease](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java#beginFileLease) to start automatic renewal process. [LeaseRenewer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/client/impl/LeaseRenewer.java) is a daemon thread to check if the renewal is expired. 
* if Erasure Coding is enabled, a DFSStripedOutputStream instance will be created, otherwise a DFSOutputStream instance will be created(Data Streamer instance will be created when EC is not enabled).
* Once DFSOutputStream instance created, DFSClient computes how many chunks and packets will be sent.
* When EC is not enabled, [DataStreamer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DataStreamer.java) will take the responsibilities for sending data packets to the datanodes in the pipeline.

#### Communication protocol
* [DFSClient](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java) holds a retry policy if 10 times retries are not reached.
* DFSClient uses [ClientProtocol](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/protocol/ClientProtocol.java) to make a RPC call.
* [NameNodeRpcServer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNodeRpcServer.java) takes the RPC call from DFSClient. 

#### Prepare for new file.

* Once [NameNodeRpcServer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNodeRpcServer.java) get create RPC call from DFSClient, first it will check if name node is up and do sanity check for create file path length. It calls [FSNamesystem.startFile](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamesystem.java#startFile) to create a new file.
* [FSNamesystem.startFile](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamesystem.java#startFile) will call [FSNamesystem.startFileInt](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamesystem.java#startFileInt) to create file.
* [FSNamesystem.startFileInt](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamesystem.java#startFileInt) starts with checking validity of the given file path and permission, then it acquires a R/W lock(ReentrantReadWriteLock) for writing the data.
* [FSNamesystem.startFileInt](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamesystem.java#startFileInt) also checks name node is not in safe mode, create inode for the file, check if replication factor when EC is not enabled, check block size is valid, acquires FileEncryptionInfo if EZ policy is given. 
* At the end [FSNamesystem.startFileInt](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamesystem.java#startFileInt) checks if the associated path existed, create path if the path doesn't exist by calling [FSDirWriteFileOp.startFile](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSDirWriteFileOp.java#startFile).
* [FSNamesystem.startFileInt](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamesystem.java#startFileInt) write edit log for file creation event when skipSync flag is false. 
* After calling [FSNamesystem.startFileInt](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamesystem.java#startFileInt), [NameNodeRpcServer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNodeRpcServer.java) will add audit log for file creation event.(Audit log is human readable log)
* [NameNodeRpcServer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNodeRpcServer.java) returns a [HdfsFileStatus](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/protocol/HdfsFileStatus.java) instance once the file created successfully.

#### Writing

* When EC is not enabled, [DataStreamer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DataStreamer.java) will take the responsibilities for sending data packets to the datanodes in the pipeline
* DataStreamer is a daemon thread that opens streams to data node, and closes them.
* Every DataStreamer hold one dataQueue
* DataStreamer peoridacally checks dataQueue until steamer closed or dfsClient.clientRunning is false(calling DataStreamer.close will set false).
* DataStreamer will send heart beat packet if no data is available in dataQueue. 
* DataStreamer thread also will force itself to sleep if writing pipeline is congested.
* Once DataStreamer read data from dataQueue, it first checks current stage of writing. If current stage is PIPELINE_SETUP_CREATE, DataStreamer calls [nextBlockOutputStream](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DataStreamer.java#nextBlockOutputStream) to create a new block
* [nextBlockOutputStream](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DataStreamer.java#nextBlockOutputStream) calls [locateFollowingBlock](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DataStreamer.java#locateFollowingBlock) then [DFSOutputStream.addBlock](hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSOutputStream.java#addBlock) that create a new block.
* [DFSOutputStream.addBlock](hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSOutputStream.java#addBlock) makes a RPC call to [NameNodeRpcServer.addBlock](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNodeRpcServer.java#addBlock), [NameNodeRpcServer.addBlock](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNodeRpcServer.java#addBlock) calls [FSNamesystem.getAdditionalBlock](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamesystem.java#getAdditionalBlock) to add a new block.  
* 

## Encryption in HDFS

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
 
