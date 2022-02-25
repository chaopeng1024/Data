# 2 DAG

## 1 DAG是什么

   Lineage（血统），又名 DAG（Directed Acyclic Graph，有向无环图）。
   
   血统侧重于从数据的角度描述不同RDD之间的依赖关系；DAG则是从计算的角度描述不同RDD之间的转换逻辑。
   
    DAG是Spark中对于分布式计算模型的抽象，为执行计划的优化提供更多的可能性。
   
## 2 DAG与Stage划分
   DAG的构建是通过在RDD上不停地调用算子来完成的，DAG以Actions算子为起点，从后向前回溯，以shuffleMapTask和resultTask这两种类型为划分边界，划分出不同的Stages。
   
   ![stageDivide](https://user-images.githubusercontent.com/15443165/155694968-25aa6dd9-89bd-4d4c-bd4b-284a747abe11.png)
   
   图中可以看出，在宽依赖关系处就会断开依赖链，划分stage，这里的stage1不需要计算，只需要计算stage2和stage3，就可以完成整个Job。

   **更详细的过程：**
   
   [DAGScheduler](https://github.com/apache/spark/blob/c69f08f81042c3ecca4b5dfa5511c1217ae88096/core/src/main/scala/org/apache/spark/scheduler/DAGScheduler.scala) 沿着 DAG 的尾节点一路北上，并沿途判断每一个 RDD 节点的 dependencies 属性。之后，如果判定 RDD 的 dependencies 属性是 NarrowDependency，则 DAGScheduler 继续向前回溯；若 RDD 的依赖是 ShuffleDependency，DAGScheduler 便开启“三招一套”的招式，创建 Stage、注册 Stage 并继续向前回溯。由此可见，何时切割 DAG 并生成新的 Stage 由 RDD 的依赖类型决定，当且仅当 RDD 的依赖是 ShuffleDependency 时，DAGScheduler 才会新建 Stage。

   **那么，DAGScheduler怎么判定 RDD 的依赖是 NarrowDependency还是 ShuffleDependency呢？**
   
   DAGScheduler通过RDD的 partitioner 属性来判定每个 RDD 的依赖类型，具体来说，如果子 RDD 的 partitioner 与父 RDD 的 partitioner 一致，那么 DAGScheduler 判定子 RDD 对父 RDD 的依赖属于窄依赖；相反，如果两者 partitioner 不一致，也即分区规则不同（分区规则不同则意味着一定存在数据的“重洗牌”，即 Shuffle），那么 DAGScheduler 判定子对父的依赖关系是 ShuffleDependency。


