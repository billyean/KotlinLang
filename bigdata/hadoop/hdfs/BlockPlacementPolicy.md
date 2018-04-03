`BlockPlacementPolicy decide how the data node will be choosen for writing the data.`

`There are 4 known BlockPlacementPolicy in HDFS`

* [BlockPlacementPolicyDefault](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/blockmanagement/BlockPlacementPolicyDefault.java)
* [BlockPlacementPolicyRackFaultTolerant](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/blockmanagement/BlockPlacementPolicyRackFaultTolerant.java)
* [BlockPlacementPolicyWithNodeGroup](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/blockmanagement/BlockPlacementPolicyWithNodeGroup.java)
* [BlockPlacementPolicyWithUpgradeDomain](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/blockmanagement/BlockPlacementPolicyWithUpgradeDomain.java)

## BlockPlacementPolicyDefault