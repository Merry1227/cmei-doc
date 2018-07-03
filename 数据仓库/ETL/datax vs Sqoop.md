#DataX
<https://github.com/alibaba/DataX/wiki/DataX-all-data-channels>  
支持的数据源：  

- 更侧重于业务数据库之间的数迁移
- 目前还只看到是 standalone 模式
- 使用方便，迁移任务的定义也必将灵活，但是目前只有一种数据源到另一种数据源的迁移。
- 对于大数据 hdfs 的支持不是很好。明显没有利用到 hdfs 的 locality 特性读写数据。
- 但是 reader 和 writer 接口定义非常灵活，也可以自己扩展
- transformation 好像还没有实现
- 支持分库分表的数据库

源码解读：
入口：Engine
关键：JobContainer 和 TaskGroupContainer
如何调度：
Reader的任务切分：
RangeSplit：会根据参数job.setting.speed.channel来最终确定每个表改分成几个task。简单的说，假如 channel 设置的是4，你的分库分表的总 table 为2，则每张表会获得2个 splittask。
Writer 的任务切分：跟 reader 保持一致。

#Sqoop2
##值得借鉴的：

- commandline客户端入口 shell->SqoopShell： 用的是groovysh
- API Client入口：实际上是 web api （json 格式）
- 调度和执行引擎：
- Repository：底层如何适应多种db的设计
- partition：  
  - 数字类型：Range
  - 日期类型：本质上也是 Range
  - 字符类型：利用 java unicode 将任意字符转换成对应的    codepoint， 变成BigDecimal 比较大小

  
##基础：  
字符串：
hash：TODO  
  
* 编码：任何一种语言，内存之间数据都不要考虑编码，当与外界发生交换和读取时，需要考虑外界文件的编码，再解码并成 unicode。
java,python 内存里面都是 unicode 编码，只有当需要存储或传输时则需要不同的编码，甚至包括压缩的（为了时空考虑）。所以内存大小和保持成文本大小通常肯定是不一样的。
<http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html>  
  
* 大端小端问题：跟编码无关，主要是 cpu不同型号，主要影响大于 byte 的任意数据的操作，cpu 从内存中取出数据后，是该先读高端还是低端或者从其他地方读取数据后，放入内存高端还是低端地址。
主要需要注意的是是不同操作系统之家不同语言的数据传输。
对于 java 不用处理。**网络传输时大端的**，java 也是大端。
对于下面这种情况，A 端 C 则需要出来。
操作系统 A（小端）C<->网络传输（大端）->操作系统 B（大端）java  
<http://blog.chinaunix.net/uid-12707183-id-2919249.html>  
**网络层协议规定了网络层是大端的，所以小端方在发送前或接受后都需要做相应的转换。**
<http://www.cnblogs.com/fuchongjundream/p/3914770.html>

#Spark JDBC
目前只支持 Long partition