## MongoDB故障转移和选举
### 基本概念
* replication set members：
    * primary：The primary receives all write operations.
    * secordary: Secondaries replicate operations from the primary to maintain an identical data set. 可以参与选举，  
```
是否可以变成primary	是否触发选举	是否参与投票	是否接受读请求	作用
priority0	N	N	Y（可选）	Y	多DC的情况下，作为一些特别的用途， 如：Standby, 保证只有满足特定硬件、或者地理条件的member能变成primary
Hidden(Special priority)	N	N	Y	N	reporting and backups
delayed(special Hidden)	N	N	Y	N	a delayed member can make it possible to recover from unsuccessful application upgrades and operator errors including dropped databases and collections.
```
 
 
 
    * arbiter: 可选的，参与选举但不保存数据。 To have an odd number of votes for elections.
* oplog:
 * MongoDB applies database operations on the primary and then records the operations on the primary’s oplog.
 * The secondary members then copy and apply these operations in an asynchronous process.
 
### 理论篇

###影响选举的因素和条件：
* Heartbeats：（Loss of a Data Center）
    * 每2s互做一次 heartbeat
    * 10s不返回，则认为失联

* Network Partition-脑裂
  * primary step down 降级，变成secondary
  
* Priority
    * 影响选举的时间和结果
    * 高优先级的seccondary 发起选举后，选举成功的概率更大，时间更短。
    * 允许投票的member的状态
        * PRIMARY
        * SECONDARY
        * RECOVERING
        * ARBITER
        * ROLLBACK

### 选举过程
1. 状态检测  
    状态检测触发条件：  
    复制集成员心跳检测结果发生变化,比如某个节点挂了或者新增节点.  
    超过4s没有执行状态检测过程.  

2. 状态检测过程：  
    检测自身是否处于选举过程,如果是,退出本次过程.  
    维护一个主节点的备用列表,列表中所有节点都可能被选举为主节点,每个节点都会检测自身以及全局条件是否满足:  
    是否看见复制集中是否有Majority在线.  
    自身priority大于0.  
    自身不为arbiter.  
    自身opTime不能落后于最新节点10s以上.  
    自身存储的集群程序按信息为最新.  
            如果所有条件满足,则将自身添加到主节点备用列表中,否则,将自身从列表中移除.    
        
3. 主节点降为从节点：  
    检测以下条件,若都满足,将主节点将为从节点(如果要降级的主节点是自身,直接调用降级方法,如果不为自身,调用replSetStepDown命令将复制集主节点降级为从节点.):  
    集群中主节点存在.  
    “主节点的备用列表”中存在比当前的主节点priority更高的节点.  
    “主节点的备用列表”中priority最高的节点,其opTime要比其他所有节点最新的opTime落后10s以内.  
    检测自身是否为主,若为主,且自身无法看见复制集的Majority在线,将自身降级为从.  
    如果看不见集群中有主节点存在,检测自身是否在”主节点的备用列表”,若不在,打印log并退出此流程.    

4. 发送自身节点为主节点通知  
    若自身在”主节点的备用列表”中,开始判断自身可否向复制集中发送选举自身为主节点的通知,判断过程包含:  
    自身是否可以看见复制集中的Majority在线.  
    自身是否在”主节点的备用列表”.  
    若条件满足,则设置”自身已经在选举过程中”标识位为true,并进入”选举自身为主节点”方法：  
    此线程拿到了线程锁.  
    此节点没有被配置slaveDelay选项或者配置的slaveDelay为0.  
    此节点没有被配置为arbiter.  
    若满足,则调用环境检测,若以下条件被触发,则不发送“选举我为主节点”投票:  
    当前时间小于steppedDown的结束冻结时间(为执行steppedDown时的时间+冻结设定时间,内部调用为60s).  
    自己的opTime不是所有节点最新的.  
    若有节点opTime比自己新,直接退出此流程.  
    如果其他最新的节点最多与自己一样新,每有一个这样的节点,随机sleep一段时间,之后继续判断.  
    自己上线5分钟内且复制集中不是所有节点在线.？？？  


5.发起投票-”选举我为主节点”的投票  
    如无其他问题,尝试获取自己进行投票时的票数,在此过程中,会判断自己在30s内是否进行过投票,如进行过,直接退出整个过程.  
    经过以上种种复杂的检测,终于可以向复制集发送”选举我为主节点”的投票.  
    发送之后,会接收来自所有节点的投票,若得票数小于等于一半,不将自己变为主节点,若超过一半,设置自己为主节点. 投票结束后,设置”自身已经在选举过程中”标识位为false.  

6. 其他节点收到”选举我为主节点”投票信息的投票策略：  
    若自身存储的复制集配置版本过低,不投票.  
    若发起请求的节点存储的复制集配置版本过低,投反对票.  
    如果自身所在的复制集没有发起投票的节点,投反对票.  
    复制集中存在主节点,投反对票.  
    可参与选举的节点中有priority高于请求为主的节点存在时,投反对票.  
    如果所有条件通过,获取自身的投票数(同样会判断自身在30s内是否参加过投票,若参加过,不再投票),投出票数.  
