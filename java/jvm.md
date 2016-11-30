#JVM Tuning

reference: <https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/toc.html>

##Introduction

* JVM （Java HotSpot virtual machine)提供了多个GC (Garbage Collectors)
* Java SE 可以根据应用选择合适的GC，但是这种选择不一定对每个应用都是可选的？？？
* GC 是 内存管理的工具， 它可以通过下面的操作来达到自动管理内存的目的：
	- 将objects 分配到新生代，将较老的objects推入老生代
	- 通过并行的标识阶段（concurrent/parallel marking phase) 找到old generation 里面的存活的objects。 当总的堆内存占有率达到默认的阈值事，JVM将会触发这个阶段。
	- 通过并行拷贝parallel copying， 压缩live objects来提升free memory。
* 什么时候GC的选择有着重大的影响呢？
	- 对某些应用而言，也许没有任何影响。比如：在gc频率适度，gc pause的时间可以接受的情况下，应用程序能够运行良好。
	- 但是对大多数应用，特别是处理大数据量(几G)，多线程，高事务并发的应用而言，gc的选择非常重要。
 
 ## JVM tuning parameters

* Maximum Pause Time:This is interpreted as a hint to the garbage collector that pause times of <nnn> milliseconds or less are desired.
	- -XX:MaxGCPauseMillis=<nnn>
* Throughput:The throughput goal is measured in terms of the time spent collecting garbage and the time spent outside of garbage collection (referred to as application time).
	- XX:GCTimeRatio=<nnn>.
	- The ratio of garbage collection time to application time is 1 / (1 + <nnn>). For example, -XX:GCTimeRatio=19 sets a goal of 1/20th or 5% of the total time for garbage collection.
* -verbose:gc, -XX:+PrintGCDetails,-XX:+PrintGCTimeStamps
* the total heap size
	- -Xms<min>
	- -Xmx<max>
 	- -XX:MinHeapFreeRatio=<minimum>
	- -XX:MaxHeapFreeRatio=<maximum>
* the young generation size
	* NewRatio: the ratio between the young and tenured generation is 1:3 -XX:NewRatio=3
	* NewSize:the size from below
	* MaxNewSize:the size from above
* Survivor Space Sizing
	- SurvivorRatio: -XX:SurvivorRatio=6 sets the ratio between eden and a survivor space to 1:6
	- XX:+PrintTenuringDistribution
* Available Collectors
	- -XX:+UseSerialGC
	- -XX:-UseParallelOldGC
	- -XX:+UseConcMarkSweepGC
	- -XX:+UseG1GC
* java -XX:+PrintFlagsFinal <GC options> -version | grep MaxHeapSize