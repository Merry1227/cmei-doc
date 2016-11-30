Yarn Applciation Diagnosis 一般步骤
1. Application Master 是否已经启动，Application的状态是否是Accept

  如果application未被accept，根据yarn client得到的diagnotic信息，查看原因：

16/02/22 22:06:07 INFO yarn.Client: 
  client token: N/A
  diagnostics: N/A
  ApplicationMaster host: N/A
  ApplicationMaster RPC port: -1
  queue: q01
  start time: 1456207569767
  final status: UNDEFINED
  tracking URL: http://vm-slaver1:8088/proxy/application_1456207530505_0001/
  user: hadoop

2.