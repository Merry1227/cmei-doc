#slf4j,log4j,logback
<http://www.cnblogs.com/zhuawang/p/3999235.html>
<http://www.cnblogs.com/Sinte-Beuve/p/5758971.html>
#log4j
##logger,appender,layouts
  logger,appender,layouts是log4j的三个重要的组件。三者结合起来，根据 log 的 message type 和 level 来控制 format message 的输出。  
  
  * logger:是层次化结构的。顶级层的Logger，它提供Logger对象。Logger对象负责捕获日志信息及它们存储在一个空间的层次结构。
  * layouts:该层提供其用于格式化不同风格的日志信息的对象。布局层提供支持Appender对象到发布日志信息之前。布局对象的发布方式是人类可读的及可重复使用的记录信息的一个重要的角色。
* Appender:Appender对象负责发布日志信息，以不同的首选目的地，如数据库，文件，控制台，UNIX系统日志等。
 
  <http://logging.apache.org/log4j/1.2/manual.html>
  <http://www.yiibai.com/log4j/>
  
##配置文件
  <http://blog.csdn.net/azheng270/article/details/2173430/>
##pattern配置
  <http://blog.csdn.net/guoquanyou/article/details/5689652>
##配置例子
  
```
#logger settings
log4j.rootLogger=DEBUG,console1
#,file1,dailyRollingFile1,rollingFile1,writer1,syslog
log4j.logger.cmei.logger1=DEBUG,dailyRollingFile1
log4j.logger.cmei=DEBUG,dailyRollingFile1
log4j.logger.cmei.logger2=INFO,rollingFile1

#####appender类型#################################
###org.apache.log4j.ConsoleAppender（控制台)
#org.apache.log4j.FileAppender（文件)
#org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）
#org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件)
#org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）
#org.apache.log4j.net.SyslogAppender (log4j的日志信息发送到远程linux服务器的syslog中)
#import org.apache.log4j.net.SocketAppender;
################################################

###################layout类型######################
#org.apache.log4j.HTMLLayout（以HTML表格形式布局)
#org.apache.log4j.PatternLayout（可以灵活地指定布局模式），
#org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串），
#org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）
#################################################

################LayoutPattern####################
#   %m   输出代码中指定的消息
#　　%p   输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL
#　　%r   输出自应用启动到输出该log信息耗费的毫秒数，不是太有意义
#　　%c   列出logger名字空间的全称,加上{h} 表示层次
#    %C   列出调用 logger 类的全名。
#　　%t   输出产生该日志事件的线程名
#　　%n   输出一个回车换行符，Windows平台为“/r/n”，Unix平台为“/n”
#　　%d   输出日志时间点的日期或时间，默认格式为ISO8601(2016-12-22 14:58:00,779)，也可以在其后指定格式，比如：%d{yyy MMM dd HH:mm:ss , SSS}，输出类似：2002年10月18日  22 ： 10 ： 28 ， 921
#　　%l   输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main(TestLog4.java: 10 )
#    实际上有%l,%t不是太有必要了。
#   填充： %5p 右对其填充 %-5p左对其填充
#########layout examples#########################
#一般格式：
#日期 错误级别，logger名 日志发生的位置 具体的 message回车
#%d  %-5p %c{2} %l %m%n
#################################################

#console appender settings
log4j.appender.console1=org.apache.log4j.ConsoleAppender
#默认System.out,可不配
log4j.appender.console1.target=System.out
log4j.appender.console1.Encoding=UTF-8
log4j.appender.console1.layout=org.apache.log4j.PatternLayout
log4j.appender.console1.layout.ConversionPattern=%d{yy-MM-dd HH:mm:ss} %-5p %c{2} %l %m%n

#daily rolling file appender
log4j.appender.dailyRollingFile1= org.apache.log4j.DailyRollingFileAppender
log4j.appender.dailyRollingFile1.File = dailyRollingFile.log
log4j.appender.dailyRollingFile1.Append = true
log4j.appender.dailyRollingFile1.Threshold = INFO
log4j.appender.dailyRollingFile1.layout = org.apache.log4j.PatternLayout
log4j.appender.dailyRollingFile1.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n

# size rolling file appender settings
log4j.appender.rollingFile1=org.apache.log4j.RollingFileAppender
#指定日志消息的输出最低层次。
log4j.appender.rollingFile1.Threshold=INFO
#指定消息输出的文件路径。
log4j.appender.rollingFile1.File=rollingfilelog.log
#默认值是true,即将消息增加到指定文件中，false指将消息覆盖指定的文件内容。
log4j.appender.rollingFile1.Append=true
#后缀可以是KB, MB 或者是 GB. 在日志文件到达该大小时，将会自动滚动，即将原来的内容移到filelog.log.1
log4j.appender.rollingFile1.MaxFileSize=1024MB
log4j.appender.rollingFile1.MaxBackupIndex=10
log4j.appender.rollingFile1.layout=org.apache.log4j.PatternLayout
log4j.appender.rollingFile1.layout.ConversionPattern=%d{yy/MM/dd HH:mm:ss} %p %c{2}: %m%n
```
##扩展log4j
自定义 layout，只输出 error trace的最外层。
<https://github.com/Merry1227/spring-practice/blob/master/spring-basic/src/main/java/cmei/demologs/MyPatternLayout.java>

#LogBack
<http://blog.csdn.net/haidage/article/details/6794509>
<http://blog.csdn.net/haidage/article/details/6794529>

##pattern
* c{length} or lo{length} or logger{length}
* C{length} or class{length}
* contextName or cn
* d{pattern} or date{pattern}
* F or file 输出执行记录请求的 java 源文件名。尽量避免使用，除非执行速度不造成任何问题。
* caller{depth} or caller{depth,evaluator-1,...,evaluator-n}
 输出生成日志的调用者的位置信息，整数选项表示输出信息深度
* L or Line:输出执行日志请求的行号。
* m or msg or message: 输出应用程序提供的信息。
* M or method: 输出执行日志请求的方法名。
* n
* p or le or level
* r or relative:输出从程序启动到创建日志记录的时间，单位是毫秒
* t or thread
* replace(p){r,t}:
* p 为日志内容，r 是正则表达式，将p 中符合r 的内容替换为t 。例如， "%replace(%msg){'\s', ''}"

格式修饰符：  

* -：左对齐
* 无：右对齐填充
* .:截断 .10 从前面开始截断大于(限定宽度）的部分
* 数字：宽度

##Filter
设置各种过滤策略
<http://logback.qos.ch/manual/filters.html>