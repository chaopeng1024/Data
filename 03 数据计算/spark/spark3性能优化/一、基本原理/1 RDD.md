## RDD
1、RDD是什么

    RDD（Resilient Distributed Dataset）叫做弹性分布式数据集，是Spark中最基本的数据模型抽象，它代表一个不可变、可分区、可并行计算的集合。

2、RDD两大特性  

    RDD两大特性：分布式和容错性。

  RDD靠什么来实现两大特性？ RDD的五大属性。 [code](https://github.com/apache/spark/blob/c69f08f81042c3ecca4b5dfa5511c1217ae88096/core/src/main/scala/org/apache/spark/rdd/RDD.scala) 
  |  属性 | 属性含义 | 成员类型 | RDD特性 | 刻画方向 |
  | ----|--------- | --------|----- |----- |
  | compute             | 生产该RDD的计算方法     | 方法 | 容错性 | 纵向 |
  | dependencies        | 该RDD依赖的父RDD       | 变量 | 容错性 | 纵向 |
  | partitions          | RDD的所有数据分片实体   | 变量 | 分布式 | 横向 |
  | partitioner         | 分区器：数据分片的规则   | 方法 | 分布式 | 横向 |
  | preferredLocations  | 存取分片的优先位置      | 变量 |       |      |
  
    横向属性partitions和partitioner锚定数据分片实体，并且规定了数据分片在分布式集群中如何分布；
    纵向属性dependencies和compute用于在纵深方向构建DAG，通过提供重构RDD的容错能力保障内存计算的稳定性。

3、RDD的两大基本操作

   RDD支持的两大算子操作： [Transformation](https://spark.apache.org/docs/latest/rdd-programming-guide.html#transformations) 和[Action](https://spark.apache.org/docs/latest/rdd-programming-guide.html#actions)。
    
   Transformation主要做的是就是将一个已有的RDD生成另外一个RDD。Transformation具有lazy特性(延迟加载)。只有当我们的程序里面遇到一个action算子的时候，Transformation算子代码才会真正的被执行。
   
   Action主要是触发代码的运行，一段spark代码里面至少需要有一个action操作。





4、宽依赖和窄依赖
