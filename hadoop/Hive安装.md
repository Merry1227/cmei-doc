# Hive 安装
<http://blog.itpub.net/28929558/viewspace-1192693/>


注意：  

* 需要在 hdfs 下面创建/user/hive/warehouse
* 需要手动添加mysql connector 到 hive lib
* 如果是本地安装，需要修改 conf/hive-site.xml 里面的system:java.io.tmpdir  将其替换成本地的 tmp 目录。({system:user.name} 替换)
如果是集群的话，需要在 hdfs 创建/tmp
* jdbc连接串：jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&amp;useSSL=false 




# Hive on Spark
<http://www.cnblogs.com/linbingdong/p/5806329.html>
# Hive Mysql 中文乱码问题
hive metadata初始化时使用的是latin1, 需要手动改db，所有表的默认字符集utf-8.
comment 中文乱码，需要修改表COLUMNS_V2列COLUMN_NAME
<http://blog.csdn.net/xiao_jun_0820/article/details/32136581>
<http://blog.csdn.net/pzw_0612/article/details/48087425>


Exceptions:
1. Exception in thread "main" java.lang.RuntimeException: org.apache.hadoop.hive.ql.metadata.HiveException: java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient
    * 版本问题 Connector 版本问题
    * schema version检测带来的问题，调用schematool -dbType mysql -initSchema 初始化schema 
    https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool
    http://www.cloudera.com/documentation/cdh/5-1-x/CDH5-Installation-Guide/cdh5ig_hive_schema_tool.html
