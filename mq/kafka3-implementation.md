# Kafka 实现要点笔记

## Kafka Producer

### Kafka Producer client主要解决哪些问题:

## Kafka Consumer

### Kafka Consumer client主要解决哪些问题：
* 与Kafka服务器之间的网络连接的管理、心跳检测、请求超时重试等底层操作.
* 订阅topic的分区数量、分区leader副本的网络拓扑.
* Consumer Group的rebalance.
* 自动提交offset、 offset的记录.  

 **早期offset记录在zookeeper中， 为缓解zookeeper压力，新版本保存在一个特殊的内部Topic(__consumer_offsets)中。**

###Exactly Once的语义保证
* 需要消费者和生产者两部分共同决定
生产者：
    * 生产者端利用全局id产生器产生消息的全局id
    * 每个分区只有一个生产者写入，当出现异常或者超时的时候，生产者就要查询此分区的最好一个消息，
    用了决定后续操作是消息重传还是继续发送。
消费者：
    * 可选方案：关闭自动提交，不再存入offset里面，由消费者自己保持offset（比如存入mysql db), 
    将消息处理结果和offset的提交放入一个事务中。

