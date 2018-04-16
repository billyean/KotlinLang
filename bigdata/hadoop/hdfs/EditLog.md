# Process for startup

NameNode starts with [initialize](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNode.java#initialize) method by reading a [Configuration](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/conf/Configuration.java) instance. It first calls [loginAsNameNodeUser](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNode.java#loginAsNameNodeUser) check user's access. [initialize](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNode.java#initialize) also registers it's state as [startupProgress](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/startupprogress/StartupProgress.java) to [StartupProgressMetrics](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/startupprogress/StartupProgressMetrics.java).

NameNode could start a [HTTP Server](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNode.java#startHttpServer) if the start node is not a backup node or checkpoint node.

At thime time, [loadFromDisk](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamsystem.java#loadFromDisk) will be called for loading FSImage and EditLog from the disk. [loadFromDisk](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamsystem.java#loadFromDisk) calls [loadFSImage](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamsystem.java#loadFSImage)

## loadFSImage

[loadFSImage](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamsystem.java#loadFSImage) plays key role for loading FSImage and EditLog. It will format(destroy)
  

# Files name

- FSImage - handles checkpointing and logging of the namespace edits
- FSEditLog
- seen_txid
- VERSION

# Rolling start