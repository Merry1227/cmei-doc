# Mongo Aggregation
 **mongo 提供三种方式的aggregation:**
## pipeline:
   Mongo aggregation pipeline 由stage组成，pipeline stage 不会对每个input document输出 output document。
   （言下之意，map-reduce 会？？）。 每个操作可以理解为一个stage。 默认 aggregation 命令会逻辑是会传整个的collection到
   aggregation pipeline。为避免扫描整个集合可，以采取下面这些策略：
   1）加Index
   在pipeline开始，加入$match, $sort.
   2）尽早Filter
   3)

   使用pipeline的限制: <https://docs.mongodb.com/manual/core/aggregation-pipeline-limits/>
   1)结果集大小： 16M
   2）内存限制：100M， 使用allowDiskUse可以enable aggregation pipeline 处理更大的数据集，将数据写入临时文件。



## map-reduce function: map-reduce 效率更低，使用custom的JavaScript支持更复杂和灵活的aggregation.
## single purpose aggregation methods

