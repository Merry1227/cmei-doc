#Sql语句示例
##MySQl
* load data   
load data local infile '/home/admin/cmeitmp/diff/diff_1027206505_13_20161025'
into table sms_user fields TERMINATED by','

* create index  
create index query_method on alertAccessLog(query,method);
show index from alertAccessLog;


##Hive SQL