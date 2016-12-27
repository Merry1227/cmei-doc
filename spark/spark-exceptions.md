#Spark-Exceptions

1. java.lang.NoClassDefFoundError: org/apache/spark/Logging spark_jar
org.apache.spark.scheduler.cluster.CoarseGrainedClusterMessages ocal class incompatible: stream classdesc serialVersionUID . 版本冲突

2. 一般问题：
<https://databricks.gitbooks.io/databricks-spark-knowledge-base/content/troubleshooting/connectivity_issues.html>

3. empty partition:  
If you didn't expect to have empty partitions, it may be worth investigating why you have them. Empty partitions can happen either due to a **filtering** step which removed all the elements from some partitions, or due to a **bad hash function**. If the hashCode() for your RDD's elements doesn't distribute the elements well, it's possible to end up with an unbalanced RDD that has empty partitions.

4. Server Problem, we ran into problems retreving data from server, please try again later.

5. driver program 与 spark 集群版本不一致（1.6.0-1.6.2）
java.io.InvalidClassException: org.apache.spark.rdd.MapPartitionsRDD; local class incompatible: stream classdesc serialVersionUID = -1059539896677275380, local class serialVersionUID = 6732270565076291202

6. Yarn Support Exception on Driver:add dependencies

7. Yarn Application started:
Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/spark/Logging
spark.yarn.jar=hdfs://

8. java.lang.IllegalArgumentException: Required executor memory (1024+384 MB) is above the max threshold (1024 MB) of this cluster! Please check the values of 'yarn.scheduler.maximum-allocation-mb' and/or 'yarn.nodemanager.resource.memory-mb'.

9. spark ui didn’t work on yarn mode 
  症状：4040 端口正常，但是全部重定向到 /null
16/09/13 02:09:02 WARN server.AbstractHttpConnection: header full: java.lang.RuntimeException: Header>6144
16/09/13 02:09:02 WARN server.AbstractHttpConnection: /null/null/null/null/null/null/…./
spark 版本，实质可能应该是 spark 用的 yarn 版本，与集群版本的冲突，**关键在 yarn 的application master url代理重定向不对**
spark-assembly包是 spark 1.6.2 hadoop1.6.0
但是集群环境： hadoop1.6.4 

10. 16/09/13 05:30:46 WARN scheduler.TaskSetManager: Lost task 0.0 in stage 0.0 (TID 0, ash-109-35l.labs.microstrategy.com): java.lang.ClassNotFoundException: org.apache.spark.SparkContext$$anonfun$hadoopFile$1$$anonfun$34
spark.yarn.jar= (版本问题）


11. /home/hadoop/installed/hadoop-2.6.4/sbin/yarn-daemon.sh: line 125: /tmp/yarn-hadoop-resourcemanager.pid: Permission denied
 查看 /tmp 权限，chmod 777 /tmp as root account

12. spark application 起来之后又被 kill， signal 15， SIGTERM. 
16/08/28 21:17:29 ERROR yarn.ApplicationMaster: RECEIVED SIGNAL 15: SIGTERM

13. 16/09/14 11:38:47 ERROR controller.LightningServer: Permission denied: user=canhuamei, access=WRITE, inode="/":hadoop:supergroup:drwxr-xr-x

14. ERROR Executor: Exception in task 0.0 in stage 6.0 (TID 5)
com.esotericsoftware.kryo.KryoException: Encountered unregistered class ID: 13994
	at com.esotericsoftware.kryo.util.DefaultClassResolver.readClass(DefaultCla
streaming做了 checkpoint，application 代码发生了变化。需要删除 checkpoint 文件。




