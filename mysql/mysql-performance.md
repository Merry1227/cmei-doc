#MySQL Manual 读书笔记
最近在看mysql manual 里面的第八章Optimizaiton.感觉帮助最大的就是索引优化，故记录下来。

参考：<http://dev.mysql.com/doc/refman/5.6/en/optimization-indexes.html>

* 对SELECT 操作而言，最有效的提供性能的方法就是创建索引。基本上mysql的所有数据类型都可以创建索引。

下面这篇博文很好的总结了索引优化的概念和原则，推荐先读一下：

<http://www.cnblogs.com/ggjucheng/archive/2012/11/11/2765428.html>

<http://www.cnblogs.com/hustcat/archive/2009/10/28/1591648.html>

下面我将总结一下博文中没有提到的部分。

##index的存储结构

大部分的indexes（primary key,unique,index, fulltext)都是存在**B-tree**中。

例外：空间数据(spatial data)存在R-tree中；memory tables存在hash index中；InnoDB使用inversted list存储fulltext。

这只是基本规则，具体的得看storage engine。或者用命令**show index from tablename**查看index_type.

在这，不了解这些数据结构的可以去看下：

<http://www.cnblogs.com/taxuexunmei/articles/4363599.html>

###B-tree vs Hash index

1. 最重要的，B-tree可用于各种比较：包括=，>,<,>=,<=,between 等，而hash则只可用于equality 比较：= 或者<=>

2. 类似的，hash index不能提高order by操作的效率。

3. 在hash index中仅能整个key用来查找某行，而在B-tree中，可以用任何一个key的最左边的前缀 (leftmost prefix)来查找：
	* like 语句中，对于index列的查找，前缀为常量而不是“%“的查找可以更快。如” key_col like 'Pat%_ck%'。
	* 对于多列索引，mysql仅对index的leftmost prefix执行索引查询.比如索引为（col1,col2,col3),则（col2),(col2,col3)不是leftmost prefix，故不会用到索引查询，而(col1),(col1,col2),(col1,col2,col3)则会。  
       
	```
	 select * from table_name
	 where col1=val1; //col1=val1 and col2=val2 会
	      
	 select * from table_name
	 where col2=val2;/col2=val2 and col3=val3; 不会  
	```
	* 如果index的leftmost前缀必须出现在每个AND group中，才会用到索引. 
  如：下面的例子会用到index  

	```
	...where index_part1=1 and index_part2=2 and other_col=3;
	...where index=1 or A=10 and index=2;
	...where index1=1 and index2=2 or index1=3 and index3=3;// index1会用到index 而index2和index3则不会。
	```
下面的则不会：  

	```
	... where index_part2=1 and index_part3=2; //index的leftmost前缀index_part1没有使用，
	....where index=1 or A=10;
	...where index_part1=1 or index_part2=10;
	```

##哪些operations中考虑用index：

* **为了更快地找到符合where cause的行。**
* **减少可能的行的候选。**
* **多列组合索引时，最左前缀的索引可用于优化器查找。**
* **当join时，需要从其他表中获取列。如果join的列定义成相同类型且相同的大小的话，在改列使用index更高效。**  
   * varchar和char被认为是相同类型，但varchar(10)和char(15) size不同，varchar(10)和char(10)则是same data type and size。
   * 此外如果是nonbinary的string列，还需要character set相同。
* 查找知道索引列的max（）或min（）。如：  

```
select min(key_part2),max(key_part2) from tbl_name where key_part1=10.
```

* order by key_part1,key_part2 需要匹配leftost prefix
* 只选择索引列，如select key_part2 from tbl_name where key_part1=1
* 如果query的行数占整个表的绝大部分的话，不用用索引。此时，即使index可用，mysql也不一定使用。比如：当优化器estimate到如果使用index的话，仍然会需要读取表中的绝大部分的数据的时候，可能做全表扫描的话更快。 **但是，如果使用了LIMIT 去获取少量数据的话，则依然会用index。**
* 表本身比较小的话，也没必要建index。

##其他补充

###Primary key

* 如果某个自己重要且数据量比较大，但是却没有名列的column或者columns可以作为PK，可以单独创建一个anto-increment的列作为pk，这样当用FKjoin 表时，该列可以作为其他表中相应列的pointer。

### Foreign Key

* 如果某个表的列比较多，而你需要query不同列的组合，那么将该表中不长使用的数据和列分离出去，建立关联表可能更为高效。（即使存在冗余）由于相关的列被pack在一起放在disk上，可能会减少io或者占用更少的cache memory。

### Column indexes

* 所有的storage engines都应该至少支持：16 indexes per table，256bytes 长度的index。大多数storage engine的limit大于这个值。
* 当在Blob或者TEXT上建立索引时，必须指定index的前缀长度。如：create table test (blob_col BLOB,index(blob_col(10))
* spatial index：MyISAM支持R-tree index， 其他引擎支持的是B-tree index。
* 对于多列的组合索引，可以基于这些columns引入一个hashed的列，如果该列short，reasonably unique and indexed, 有可能会比多列上建立的 “wide” index更快。  
如：select * from tbl_name where hash_col=md5(concat(val1,val2)) and col1=val1 and col2=val2;

##用Explain 语句验证index的使用

<http://www.cnblogs.com/ggjucheng/archive/2012/11/11/2765237.html>

##mysql 索引的CRUD

<http://www.cnblogs.com/tianhuilove/archive/2011/09/05/2167795.html>