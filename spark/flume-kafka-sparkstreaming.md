#spark streaming+kafka integration
<http://spark.apache.org/docs/latest/streaming-kafka-0-8-integration.html#approach-2-direct-approach-no-receivers>

* Approach 1: Receiver-based Approach 
spark 1.2 中主要使用 Receiver 来接受数据，receiver是通过 kafka 的 consumer api 来实现的。为保证无数据丢失，需要开启 write ahead log。
	* consumed offset 是利用 kafka 的 api 保存在 zookeeper 里面。来保证 at least once。不一致可能发生在 spark streaming 可靠的收到数据zookeeper 记录的 offset 之间。
* Approach 2: Direct Approach (No Receivers)
Spark 1.3 以后提供了不依赖 Receiver 的直接方式来周期性 query kafka 获取最新的 offset，然后在每个 batch 里面生产相应的offset range，然后通过 kafka 的 consumer api 读取对于offset range 的数据。
	* 更直观，并行方式更简单，kafka 和 rdd Partition 直接的 one-to-one mapping 的关系。
	* 效率更高，为保证 zero-data loss，approach 1方式在 kafka 和 spark write ahead log里面被 replicate两次。在 approach2中，spark 无需保存数据了，只有 kafka 的 retention 够，可以从 kafka 再重新读取丢失的数据。
	* 保证了exactly-once，无需 zookeeper 参与，offset 由 sparkstreaming 的 checkpoint 记录。这保证了每条记录被 spark streaming 接收一次（exactly-once）。这种方法的劣势是不能更新 zookeeper 的 offset 信息。这样 一些 zookeeper-based 的 kafka 的 monitoring tools 就不能显示 progress。（可自行程序里面更新 zookeeper）

<http://spark.apache.org/docs/latest/streaming-kafka-0-10-integration.html>
	