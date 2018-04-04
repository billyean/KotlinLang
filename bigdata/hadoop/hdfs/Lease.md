## Client 
[LeaseRenewer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/client/impl/LeaseRenewer.java) is a daemon thread that is responsible for renewing file-being-written leases on the namenode.

Whenever a file is opened for write (create or append), namenode stores a file lease for recording the identity of the writer. 
The writer (i.e. the DFSClient) is required to renew the lease periodically. 

When the lease is not renewed before it expires, the namenode considers the writer as failed and then it may either let another writer to obtain the lease or close the file.

Usually one LeaseRenewer instance is sharing between multiple DFSClients if DFSClients were owned by same user.
 
Any time when DFSCLient need to write data, like [create](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java#create) a file, [append](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java#append) data to a file, it calls [beginFileLease](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java#beginFileLease) to add itself to [LeaseRenewer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/client/impl/LeaseRenewer.java)

[LeaseRenewer.renew](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/client/impl/LeaseRenewer.java#renew) call first dedup the DFSClient in the copies, then calls [DFSclient.renewLease](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java#renewLease) to issue renewLease RPC call to name node. 
## Server

LeaseManager is responsible for lease housekeeping for writing on files. LeaseManager has two functions

* Renew lease - Renew the lease(s) held by the given client.
* Recover lease -  