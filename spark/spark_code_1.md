spark源码
#入口 
* spark shell 入口： 
* driver program入口：SparkContext
* worker 入口：Worker.scala
* driver ui 入口：
* (SparkContext)->SparkUI.scala->WebUI.scala
	- jetty server 的启动：bind()->startJettyServer
* cluster ui 入口：
* yarn scheduler 入口：
* network 层入口：
	* driver 端
	* worker 端

#Scheduler
SparkContext 初始化时创建.  
入口：  
 
```
    // Create and start the scheduler
    val (sched, ts) = SparkContext.createTaskScheduler(this, master, deployMode)
    _schedulerBackend = sched
    _taskScheduler = ts
    _dagScheduler = new DAGScheduler(this)
    _heartbeatReceiver.ask[Boolean](TaskSchedulerIsSet)

    // start TaskScheduler after taskScheduler sets DAGScheduler reference in DAGScheduler's
    // constructor
    _taskScheduler.start()
```

##SchedulerBackend:
A backend interface for scheduling systems that allows plugging in different ones under TaskSchedulerImpl. We assume a Mesos-like model where the application gets resource offers as machines become available and can launch tasks on them.   

更高层的scheduler,负责实现 mesos,yarn 等接口。 也就是对应了master 的配置，不同模式下的scheduler策略。  

* standalone：StandaloneSchedulerBackend (CoarseGrainedSchedulerBackend)
* yarn-cluster:YarnClusterSchedulerBackend (YarnSchedulerBackend)
* yarn-client: YarnClientSchedulerBackend (YarnSchedulerBackend)
* mesos: CoarseMesosSchedulerBackend，MesosSchedulerBackend (CoarseGrainedSchedulerBackend)
* local：LocalSchedulerBackend


##DAGScheduler: 
 
将任务分解成 stage（TaskSets），然后调用 TaskScheduler 进行 Task level 的 scheduler。这里最主要的关注submitJob（）核心实现。查看一个 job 是如何分解，如果提交的。

* The high-level scheduling layer that implements stage-oriented scheduling. It computes a DAG of stages for each job, keeps track of which RDDs and stage outputs are materialized, and finds a minimal schedule to run the job.
*  It then submits stages as TaskSets to an underlying TaskScheduler implementation that runs them on the cluster. A TaskSet contains fully independent tasks that can run right away based on the data that's already on the cluster (e.g. map output files from previous stages), though it may fail if this data becomes unavailable.
*  the DAGScheduler also determines the preferre locations to run each task on, based on the current cache status, and passes these to the low-level TaskScheduler.

两个重要问题：
1.Task 的相关信息dispatch传输到 executor 上的，居然是通过 sc.broadcast?   
2. 调用 taskscheduler 去 submit TaskSet
```
 // TODO: Maybe we can keep the taskBinary in Stage to avoid serializing it multiple times.
    // Broadcasted binary for the task, used to dispatch tasks to executors. Note that we broadcast
    // the serialized copy of the RDD and for each task we will deserialize it, which means each
    // task gets a different copy of the RDD. This provides stronger isolation between tasks that
    // might modify state of objects referenced in their closures. This is necessary in Hadoop
    // where the JobConf/Configuration object is not thread-safe.
    var taskBinary: Broadcast[Array[Byte]] = null
    try {
      // For ShuffleMapTask, serialize and broadcast (rdd, shuffleDep).
      // For ResultTask, serialize and broadcast (rdd, func).
      val taskBinaryBytes: Array[Byte] = stage match {
        case stage: ShuffleMapStage =>
          JavaUtils.bufferToArray(
            closureSerializer.serialize((stage.rdd, stage.shuffleDep): AnyRef))
        case stage: ResultStage =>
          JavaUtils.bufferToArray(closureSerializer.serialize((stage.rdd, stage.func): AnyRef))
      }

      taskBinary = sc.broadcast(taskBinaryBytes)
    } catch {
      // In the case of a failure during serialization, abort the stage.
      case e: NotSerializableException =>
        abortStage(stage, "Task not serializable: " + e.toString, Some(e))
        runningStages -= stage

        // Abort execution
        return
      case NonFatal(e) =>
        abortStage(stage, s"Task serialization failed: $e\n${Utils.exceptionString(e)}", Some(e))
        runningStages -= stage
        return
    }

    val tasks: Seq[Task[_]] = try {
      stage match {
        case stage: ShuffleMapStage =>
          partitionsToCompute.map { id =>
            val locs = taskIdToLocations(id)
            val part = stage.rdd.partitions(id)
            new ShuffleMapTask(stage.id, stage.latestInfo.attemptId,
              taskBinary, part, locs, stage.latestInfo.taskMetrics, properties, Option(jobId),
              Option(sc.applicationId), sc.applicationAttemptId)
          }

        case stage: ResultStage =>
          partitionsToCompute.map { id =>
            val p: Int = stage.partitions(id)
            val part = stage.rdd.partitions(p)
            val locs = taskIdToLocations(id)
            new ResultTask(stage.id, stage.latestInfo.attemptId,
              taskBinary, part, locs, id, properties, stage.latestInfo.taskMetrics,
              Option(jobId), Option(sc.applicationId), sc.applicationAttemptId)
          }
      }```

##TaskScheduler
 
核心还是TaskSchedulerImpl
  
* standalone：TaskSchedulerImpl
* yarn-cluster:YarnClusterScheduler （TaskSchedulerImpl）
* yarn-client: YarnScheduler （TaskSchedulerImpl）
* mesos: TaskSchedulerImpl
* local：LocalSchedulerBackend

由DAG Scheduler 调用过来的submitTask  

```
  override def submitTasks(taskSet: TaskSet) {
    val tasks = taskSet.tasks
    logInfo("Adding task set " + taskSet.id + " with " + tasks.length + " tasks")
    this.synchronized {
      val manager = createTaskSetManager(taskSet, maxTaskFailures)
      val stage = taskSet.stageId
      val stageTaskSets =
        taskSetsByStageIdAndAttempt.getOrElseUpdate(stage, new HashMap[Int, TaskSetManager])
      stageTaskSets(taskSet.stageAttemptId) = manager
      val conflictingTaskSet = stageTaskSets.exists { case (_, ts) =>
        ts.taskSet != taskSet && !ts.isZombie
      }
      if (conflictingTaskSet) {
        throw new IllegalStateException(s"more than one active taskSet for stage $stage:" +
          s" ${stageTaskSets.toSeq.map{_._2.taskSet.id}.mkString(",")}")
      }
      schedulableBuilder.addTaskSetManager(manager, manager.taskSet.properties)

      if (!isLocal && !hasReceivedTask) {
        starvationTimer.scheduleAtFixedRate(new TimerTask() {
          override def run() {
            if (!hasLaunchedTask) {
              logWarning("Initial job has not accepted any resources; " +
                "check your cluster UI to ensure that workers are registered " +
                "and have sufficient resources")
            } else {
              this.cancel()
            }
          }
        }, STARVATION_TIMEOUT_MS, STARVATION_TIMEOUT_MS)
      }
      hasReceivedTask = true
    }
    backend.reviveOffers()
  }    

```

##Worker端
Worker 作为主进程，管理了多个 executor 的信息和启动
启动一个 workThread来启动ExecutorRunner,具体负责 Executor 进程的启动。
#Storage
<http://www.aboutyun.com/thread-9619-1-1.html>

把数据存储到 disk，memory 上，或者 replicate到远端。
* BlockManager 作为外部与 Storage 交互的统一的操作接口。


#Cache,Persistence
#Checkpoint
* 实际上是利用 hdfs 的冗余来实现高可用。
* 文件rdd-x保持的是 该rdd 的 信息。 
* 如果 rdd1.checkpoint(), 那么 后面依赖 rdd1的 rdd2 在计算时加载 rdd1实际上是从 checkpoint产生的eliableCheckpointRDD而来，（而不是从 rdd0->rdd1重新计算）。
* 如果 rdd1.persist()并且 checkpoint 了，会优先加载 cache 里面的，然后是 checkpoint 里面的。

##相关代码

1). 从 checkpoint 文件加载 sparkcontext，streamingContext    
  
```
SparkContext: sparkContext.setCheckpointDir()  
  
StreamingContext:getOrCreate(
      checkpointPath: String,
      creatingFunc: () => StreamingContext,
      hadoopConf: Configuration = SparkHadoopUtil.get.conf,
      createOnError: Boolean = false
    )
```    
 
2). RDD checkpoint 数据的产生（写过程） 产生     
RDD：RDD.checkpoint
ReliableRDDCheckpointData

3). RDD checkpoint 数据读取

```
  final def iterator(split: Partition, context: TaskContext): Iterator[T] = {
    if (storageLevel != StorageLevel.NONE) {
      SparkEnv.get.cacheManager.getOrCompute(this, split, context, storageLevel)
    } else {
      computeOrReadCheckpoint(split, context)
    }
  }
```
 
4). streamingcontext.checkpoint()

![checkpoint](checkpoint.jpg)


#Shuffle
Spark shuffle 是 pluggalbe的，核心接口在 ShuffleManager,可在配置文件中配置，具体在 SparkEnv 里面读取配置。  
<https://0x0fff.com/spark-architecture-shuffle/>  
<http://www.cnblogs.com/zlslch/p/5942590.html>  
* Hash-Based Shuffle：M*R个临时文件，每个 map task 一个。
  consalidate：C*R个，每个 core 产生一个大的合并文件。

* Sort-Based Shuffle：2*M 个。每个 map task产生两个，一个是 data 本身，另一个是 data index，存在 map端。reducer 端根据 index 获取自己的数据。

* Tungsten-sort Shuffle：

#RPC 层
ThreadSafeRpcEndpoint
NettyRpc	

#Spark 2.0
##SparkSession