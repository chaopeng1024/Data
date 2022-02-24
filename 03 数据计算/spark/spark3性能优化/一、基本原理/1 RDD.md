# 1 RDD
  * [1 RDD是什么](#1-rdd---)
  * [2 RDD两大特性](#2-rdd----)
  * [3 RDD两大基本操作](#3-rdd-------)
  * [4 宽依赖和窄依赖](#4--------)
  
## 1 RDD是什么

    RDD（Resilient Distributed Dataset）叫做弹性分布式数据集，是Spark中最基本的数据模型抽象，它代表一个不可变、可分区、可并行计算的集合。

## 2 RDD两大特性

    RDD两大特性：分布式和容错性。

  RDD靠什么来实现两大特性？ RDD的五大属性(3个列表，2个函数)。 [code](https://github.com/apache/spark/blob/c69f08f81042c3ecca4b5dfa5511c1217ae88096/core/src/main/scala/org/apache/spark/rdd/RDD.scala) 
  |  属性 | 属性含义 | 成员类型 | RDD特性 | 刻画方向 |
  | ----|--------- | --------|----- |----- |
  | compute             | 计算函数：生产该RDD的计算方法     | 方法 | 容错性 | 纵向 |
  | dependencies        | 依赖列表：该RDD依赖的父RDD       | 变量 | 容错性 | 纵向 |
  | partitions          | 分区列表：RDD的所有数据分片实体   | 变量 | 分布式 | 横向 |
  | partitioner         | 分区函数：数据分片的规则   | 方法 | 分布式 | 横向 |
  | preferredLocations  | 位置列表：存取分片的优先位置      | 变量 |       |      |
  
   横向属性partitions和partitioner锚定数据分片实体，并且规定了数据分片在分布式集群中如何分布。
   
   纵向属性dependencies和compute用于在纵深方向构建DAG，通过提供重构RDD的容错能力保障内存计算的稳定性。
   
   preferredLocations是实现spark的数据本地性——”移动数据不如移动计算“原则的基础，提升 I/O 效率。
  
  **本地性的分类**
  
    Process_local：Excutor进程（task线程）读取缓存在本地节点上的数据

    Node_local：读取本地节点硬盘上的数据

    Rack_local：读取存储在同一机架上的其它节点中的数据

    Any：读取非本地节点上的数据

    No_pref：数据本地性无意义，数据存储在Mysql等外部数据源，不在spark集群节点上

  数据读取速度依次递减，网络传输开销依次增大！


## 3 RDD两大基本操作

    RDD支持的两大算子操作：Transformation和Action。
    
   [Transformation](https://spark.apache.org/docs/latest/rdd-programming-guide.html#transformations) 主要做的是就是将一个已有的RDD生成另外一个RDD。Transformation具有lazy特性(延迟加载)。只有当我们的程序里面遇到一个action算子的时候，Transformation算子代码才会真正的被执行。
   
   [Action](https://spark.apache.org/docs/latest/rdd-programming-guide.html#actions) 主要是触发代码的运行，一段spark代码里面至少需要有一个action操作。

## 4 宽依赖和窄依赖

  宽依赖：父RDD的一个分区对应多个子RDD的分区。
  
  窄依赖：父RDD的一个分区只对应一个子RDD的分区。
  
 ![image](https://user-images.githubusercontent.com/15443165/155519799-b923eb38-0a70-473f-bd29-0a4b1dbd0cf0.png)

    区分宽窄依赖主要就是看父RDD的一个分区的流向，流向一个的话就是窄依赖，流向多个的话就是宽依赖。



