# Classes in data processing

* [DataXceiverServer](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/datanode/DataXceiverServer.java) is a server used for receiving/sending a block of data. it is created to listen for requests from clients or other data nodes.  it's not using RPC.
* [DataXceiver](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/datanode/DataXceiver.java) is the thread processing incoming/outgoing data stream.
