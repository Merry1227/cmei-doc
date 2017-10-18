#SSL
一般用于互联网直接传输数据加密和客户端与服务器间身份认证。会影响数据传输的性能。
<http://kb.cnblogs.com/page/162080/>
#Kerberose
域内客户端与服务间互相的身份认证。
<http://blog.csdn.net/wulantian/article/details/42418231>

##hdfs kerberose:
![img-kerberose](kerberos.png)

###Performance Impact of Security (SSL, TDE, Ranger, Kerberos, Knox)
* wire encryption inside the cluster: 2x overhead
* data encryption (Ranger KMS): 15%-20% overhead (but I guess it highly depends of what you are encrypting, not sure every single file must be encrypted).
* for Kerberos, Knox and Ranger: this is not significant and it depends of the installation and the use (network performance to KDC, number of Knox gateways, etc). regarding Ranger, since rules are "copied" locally for each service this not significant.

###Kerberos-Related Network Overhead for Large Clusters
* When Impala starts up, or after each kinit refresh, Impala sends a number of simultaneous requests to the KDC. For a cluster with 100 hosts, the KDC might be able to process all the requests within roughly 5 seconds. For a cluster with 1000 hosts, the time to process the requests would be roughly 500 seconds. Impala also makes a number of DNS requests at the same time as these Kerberos-related requests.

* While these authentication requests are being processed, any submitted Impala queries will fail, with errors like the following:

* Query Status: Couldn't open transport for hostname
    (SASL(-1): generic failure: GSSAPI Error: Unspecified GSS failure.
    Minor code may provide more information
    (Cannot contact any KDC for realm 'realm'))
* During this period, the KDC and DNS may be slow to respond to requests from components other than Impala, so other secure services might be affected temporarily.

* If you encounter this problem, consider taking one or more of these actions to address it:

* Scale your KDC implementation to support more load. Contact your KDC provider support to discuss options.

* Initiate the KDC ticket acquisition process after the cluster starts up by issuing a warm-up query. An ideal query is a SHUFFLE JOIN between two tables, so that all Impala daemons try to communicate with all others. Repeat the warm-up query until it successfully completes. For example, a 175 node cluster might take approximately 5 minutes.

* To reduce the frequency of the kinit renewal that initiates a new set of authentication requests, increase the kerberos_reinit_interval configuration setting for the impalad daemons. Currently, the default for a cluster not managed by Cloudera Manager is 60 minutes, while the default under Cloudera Manager is 10 minutes. Consider using a higher value such as 360 (6 hours).
https://www.cloudera.com/documentation/enterprise/5-8-x/topics/impala_scalability.html#kerberos_overhead_cluster_size

##Cent7上配置 KDC
<https://www.certdepot.net/rhel7-configure-kerberos-kdc/>  
<http://web.theurbanpenguin.com/configuring-a-centos-7-kerberos-kdc/>  
<http://debugo.com/kerberos/>  
<http://blog.csdn.net/kkdelta/article/details/46633557>  
###碰到的问题：  
  
* cent7没有安装rngd:
1. yum -y install rng-tools
2. 启动.
<http://web.theurbanpenguin.com/centos-7-rngd-will-not-start/>
