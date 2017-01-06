#Kafka
kafka 文档非常详细，很值得深读细度多读。先预读一遍，记下笔记。
#基本概念
kafka 集群保存了所有的published records，无论该记录是否被消费。记录被清除由配置的 retention period 决定。  

* topic,partition,offset

* Producer：
	* producer 负责 publish topics。
	* producer决策选择那些记录进入到 topic 的哪个 partition。
	* 具体的决策策略可以是 round-robin 或者根据 key 来hash.
	 
* Consumer：
	* 每个 consumer 可以在独立的进程或者独立的 machine 上。
	* consumer group：（TODO：不理解 why？？？）
		* If all the consumer instances have the same consumer group, then the records will effectively be load balanced over the consumer instances.

		* If all the consumer instances have different consumer groups, then each record will be broadcast to all the consumer processes.
	* 每个 consumer group 里面的 consumer instance 数目应该小于 Partition 数目。也即，每个 consumer 可能处理partition。
	* 每个 consumer 记录自己读的 offset。所以 consumer可以自由的 reset offset来处理之前的记录，或者 skip 某些最近的记录。
	* kafka来决策如何将 partition 分配给 consumer instances。在任意一时间点partition是 exclusive consumer被“fair share” 的。kafka protocol 动态维护某个 group 里面的 customer instance：如果有新的 customer instance 加入，则将其他成员的部分 Partition分给新成员，若某个 instance 死掉了，则会将部分 parition 分给剩下的成员。这保证了 kafka 的 scalability 特性。
	* 在 kafka 里面，publish-subscribe 中 subscribers是 一组consumer groups，是“logical subscriber”，是 consumers 的集群，而非单个的consumer进程。
	
* Partition：
	* 每个 partition 可以配置 repicate 的数目来自持容错。
	* kafka 仅保证每个 partition 内的 record 的有序性，不保证 partition 间的有序性。如果你需要全局有序处理，那么你只能有一个 Partition，每个 consumer group 只有一个 consumer instance。