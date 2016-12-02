#Exceptions汇总
常见异常
<http://wenku.baidu.com/link?url=25uwa7pitqR6685zRtnayUzRfLh6durBXjkCdIGV5stXg5oRMMT5WlqhbGw3y1GcZhiHlF3c1pDQuvqTLQAf6_cVVQjXWtbCaJezOnDvNG_>

* 2016-03-02 01:44:05,044 INFO org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor: launchContainer: [bash, /yarn/nm/usercache/hadoop/appcache/application_1454573000928_0113/container_1454573000928_0113_01_000011/default_container_executor.sh]
problems in
16/03/02 02:12:27 WARN pipeline.PipelineBus: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.security.AccessControlException): Access denied for user yarn. Superuser privilege is required

* Get the exit status for the container. （这个是正常的）
Note: This is valid only for completed containers i.e. containers with state ContainerState.COMPLETE. Otherwise, it returns an ContainerExitStatus.INVALID. 
 
ContainerId: container_1458616050498_0100_01_000004, State: RUNNING, Diagnostics: , ExitStatus: -1000,(Invalid Stutus)


* Yarn:
1.localhost:地址 不行，改成 hostname ok？？？


