# Hive 安装
<http://blog.itpub.net/28929558/viewspace-1192693/>

注意：  

* 需要在 hdfs 下面创建/user/hive/warehouse
* 如果是本地安装，需要修改 conf/hive-site.xml 里面的system:java.io.tmpdir 将其替换成本地的 tmp 目录。如果是集群的话，需要在 hdfs 创建/tmp
* jdbc连接串：jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&amp;useSSL=false

# Hive on Spark
<http://www.cnblogs.com/linbingdong/p/5806329.html>
# Hive Mysql 乱码问题
<http://blog.csdn.net/xiao_jun_0820/article/details/32136581>
<http://blog.csdn.net/pzw_0612/article/details/48087425>
