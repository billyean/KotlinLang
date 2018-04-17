# References

* [Transparent Encryption in HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/TransparentEncryption.html)

HDFS's encryption model can be divided two part

* Authentication/Authorization
* Encryption


## Acquire the encryption key.

Whenever HDFS tries to access file in an encryption zone, HDFS first need  create a EDEK for the file .

HDFS doesn't own the encryption key directly. Instead HDFS calls out to the configured KeyProvider to get EDEK by calling [EncryptionKeyInfo.getEncryptionKeyInfo](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/EncryptionKeyInfo.java#getEncryptionKeyInfo).


## Authentication/Authorization

Whenever name node gets a create/append call from client, it first acquires a [FSPermissionChecker](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSPermissionChecker.java) instance. A copule of checking will be done by this [FSPermissionChecker](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/FSPermissionChecker.java) instance.

* Parent folder writing permission check if folder's permission check is enabled and parent folder exists. 
* If parent folder does not exist, check if create flag presents
* 

## Encryption

When [DFSClient](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java) tries to write([create](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java#create) or [append](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSClient.java#append)) a file

## Permission denied

Once you see a [GSSException](https://docs.oracle.com/javase/8/docs/api/org/ietf/jgss/GSSException.html) in log, it gives a hint this may be a Permission issue. It might be better look at the [error code](https://docs.oracle.com/javase/8/docs/api/org/ietf/jgss/GSSException.html#getMajor) to learn what exactly the reason of permission issue.
