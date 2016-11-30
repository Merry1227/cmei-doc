spark源码
#入口 
* spark shell 入口： 
* driver program入口：
* worker 入口：
* driver ui 入口：
* (SparkContext)->SparkUI.scala->WebUI.scala
	- jetty server 的启动：bind()->startJettyServer
* cluster ui 入口：
* yarn scheduler 入口：
* network 层入口：
	* driver 端
	* worker 端

#Spark2.0
##SparkSession