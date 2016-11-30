#开源 hadoop 安装
##注意事项
Hadoop安装教程有很多，因此不再赘述，下面给出一些比较好的参考：

<http://www.cnblogs.com/xia520pi/archive/2012/05/16/2503949.html>

 注意事项：

1.hostname里面不要用下划线'_',但可以用'-'. 题主在这个上面犯过错，找了很久才找到原因。

2.正确的stop namenode和data node，意外的关机可能导致namenode和data node不能顺利重启，需要重现dfs format。

##chd hadoop安装

