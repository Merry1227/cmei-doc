#Kafka Design
<http://kafka.apache.org/documentation/#majordesignelements>
##Motivation
* 支持 high-throughput
* low-latency delivery

##Persistence
Don't fear the filesystem
* kafka主要依赖文件系统进行存储和 cache messages。
通常大家都会认为“disks are slow”，使得大家怀疑persistent structure是否能够提供可比的性能。实际上，disks 可能比人们想象中的快，也可能比人们想象中慢，实际上，它取决于你的使用方式，合适的 disk structure的设计经常能和 network 一样快。

对于disk的性能而言：
