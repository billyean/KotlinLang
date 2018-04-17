# FSImage and EditLog

## Why we need FSImage and EditLog

EditLog is a log that saves all the meta operations happened. Its purpose for saving all the state that next time when name node start, it can soon reaches the last place by reading from it. The purpose for FSImage is EditLog may grow too big, FSImage is the place that throw away old obsolete edit log to form a new start.

## When do the transition from EditLog to FSImage - Checkpoint

[A Guide to Checkpointing in Hadoop](https://blog.cloudera.com/blog/2014/03/a-guide-to-checkpointing-in-hadoop/)

## FSImage format

Please see comment on [FSImageFormat](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSImageFormat.java) to get basic idea the file format.

### Versioning

[FSImageFormat](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSImageFormat.java) is evolving all the times, for adding new feature for [FSImageFormat](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSImageFormat.java), [LayoutVersion](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/protocol/LayoutVersion.java) class tracks changes in the layout version of HDFS, usually a new [LayoutVersion](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/protocol/LayoutVersion.java) starts from its +1 version, but it could possible that it comes more earlier version(eg ADD_INODE_ID)

### Format

#### Header
- a magic string [HDFSIMG1]
- a MD5 hash digest.
- a 32 bit image file version.
- a 32 bit namespace id(currently we're not using it).
- number of files(64 bits)
- a 64 bits timestamp for BlockIdManager, this will be used as timestamp of last transaction that starts with.
- max sequential id
- last transaction id(64 bits)
- last allocated inode id(64 bits)
- snapshot counter(32 bits)
- number of snapshots(32)
- number of snapshot dirs(32)
- compression header
    * flag if compression is supported(8 bits)
    * codec class name if flag is true.
    
#### FSIMAGE_NAME_OPTIMIZATION 
##### With Snapshot

#### No FSIMAGE_NAME_OPTIMIZATION

# Process for startup

NameNode starts with [initialize](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNode.java#initialize) method by reading a [Configuration](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/conf/Configuration.java) instance. It first calls [loginAsNameNodeUser](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNode.java#loginAsNameNodeUser) check user's access. [initialize](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNode.java#initialize) also registers it's state as [startupProgress](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/startupprogress/StartupProgress.java) to [StartupProgressMetrics](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/startupprogress/StartupProgressMetrics.java).

NameNode could start a [HTTP Server](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNode.java#startHttpServer) if the start node is not a backup node or checkpoint node.

At thime time, [loadFromDisk](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamsystem.java#loadFromDisk) will be called for loading FSImage and EditLog from the disk. [loadFromDisk](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamsystem.java#loadFromDisk) calls [loadFSImage](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamsystem.java#loadFSImage), eventually [FSImage.loadFSImage](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSImage.java#loadFSImage) got called to load a latest image file.

## loadFSImage

[loadFSImage](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamsystem.java#loadFSImage) plays key role for loading FSImage and EditLog. It will format FSImage(destroy old one and create a clean one) if start up option was set with "-format" . Now [FSNamsystem](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSNamsystem.java) calls [FSImage.loadFSImage](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSImage.java#loadFSImage). [FSImage.loadFSImage](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSImage.java#loadFSImage) first places a R/W lock on FSImage, then it checks if it's rollback of rolling upgrade.

```java
    final EnumSet<NameNodeFile> nnfs;
    if (rollingRollback) {
      // if it is rollback of rolling upgrade, only load from the rollback image
      nnfs = EnumSet.of(NameNodeFile.IMAGE_ROLLBACK);
    } else {
      // otherwise we can load from both IMAGE and IMAGE_ROLLBACK
      nnfs = EnumSet.of(NameNodeFile.IMAGE, NameNodeFile.IMAGE_ROLLBACK);
    }
```  
 
It uses a [FSImageStorageInspector](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSImageStorageInspector.java) instance to look for get latest FSImage list, iterating this list by calling [loadFSImageFile](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSImage.java#loadFSImage). [FSImage.loadFSImageFile](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSImage.java#loadFSImageFile).


# Files name

- FSImage - handles checkpointing and logging of the namespace edits
- FSEditLog
- seen_txid
- VERSION

# Rolling start