**Kafka Exception**   
* 2017-11-08 10:45:25,043 ERROR kafka-producer-network-thread | producer-1 o.s.k.s.LoggingProducerListener:76 - Exception thrown when sending a message with key='null' and payload='{-1, 4, 11, 99, 111, 110, 116, 101, 110, 116, 84, 121, 112, 101, 0, 0, 0, 78, 34, 97, 112, 112, 108,...' to topic sleuth:
org.apache.kafka.common.errors.TimeoutException: Expiring 1 record(s) for sleuth-0 due to 30005 ms has passed since batch creation plus linger time
* Solution: 重启kafka 解决
