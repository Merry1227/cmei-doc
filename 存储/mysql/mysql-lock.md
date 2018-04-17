#MySql Lock读书笔记

##MySQL and InnoDB ACID model

ACID 是数据库设计的重要的原则，对保证商业数据和重要任务应用的reliability至关重要。MySql包含了InnoDB存储引擎这类严格服从ACIDmodel的组件，以保证数据或结果不被异常的情况（比如软件crash或硬件故障）所损坏。因此，如果你需要开发服从ACID特性的功能的话，你不需要自己再在应用中做一致性检查和故障恢复处理。如果有些情况下，你的应用可以容忍少量数据的loss或者inconsistency，你也可以调整Mysql的设置来平衡ACID reliability和更好性能的throughput。

* Atomicity:主要涉及InnoDB的transactions。相关的MySQL features包括：
	- autocommit settings
 	- commit statement
 	- rollack statment
 	- operational data from the infomation_schema tables.
* Consistency: InnoDB内部如何从crash中保护数据的处理，包括：
 	- InnoDB doublewrite buffer
	- InnoDB crash recovery
* Isolation:主要涉及InnoDB的transactions，特别是每个transaction的isolation level，包括：

	- Autocommit setting
	- set isolation level statement
	- InnoDB locking 的底层细节。在performance tuning的时候，可以通过**information_schma**看到这些细节
* Durability：可能更多的涉及mysql的特性与特点的硬件配置的交互。很多时候可能取决于cpu，network，storage devices等。

InnoDB的transaction model的目标就是：如何将多版本的db(multi-versioning)最好的属性和传统的两阶段(two-phase)锁进行结合.

提交commit意味着在当前transaction中的改变成为永久的，且对其他session可见。rollback则意味着取消当前transaction所有的改动。commit和rollcback都将释放当前transaction设置的所有锁。

##Isolation levels
* **READ UNCOMMITTED**   
存在dirty read The isolation level that provides the least amount of protection between transactions. Queries employ a locking strategy that allows them to proceed in situations where they would normally wait for another transaction. However, this extra performance comes at the cost of less reliable results, including data that has been changed by other transactions and not committed yet (known as dirty read). Use this isolation level only with great caution, and be aware that the results might not be consistent or reproducible, depending on what other transactions are doing at the same time. **Typically, transactions with this isolation level do only queries, not insert, update, or delete operations.**

* **READ COMMITTED**    
An isolation level that uses a locking strategy that relaxes some of the protection between transactions, in the interest of performance. Transactions cannot see uncommitted data from other transactions, but they can see data that is committed by another transaction after the current transaction started. Thus, a transaction never sees any bad data, but the data that it does see may depend to some extent on the timing of other transactions. When a transaction with this isolation level performs UPDATE ... WHERE or DELETE ... WHERE operations, other transactions **might have to wait**. The transaction can perform SELECT ... FOR UPDATE, and LOCK IN SHARE MODE operations without making other transactions wait.  
  
* **REPEATABLE READ**
The default isolation level for InnoDB. It prevents any rows that are queried from being changed by other transactions, thus blocking non-repeatable reads but not phantom reads. It uses a moderately strict locking strategy so that all queries within a transaction see data from the same snapshot, that is, the data as it was at the time the transaction started. When a transaction with this isolation level performs UPDATE ... WHERE, DELETE ... WHERE, SELECT ... FOR UPDATE, and LOCK IN SHARE MODE operations, other transactions might have wait.

* **SERIALIZABLE**  
The isolation level that uses the most conservative locking strategy, to prevent any other transactions from inserting or changing data that was read by this transaction, until it is finished. This way, the same query can be run over and over within a transaction, and be certain to retrieve the same set of results each time. Any attempt to change data that was committed by another transaction since the start of the current transaction, cause the current transaction to wait.

##Consistent Nonlocking Read
  MySQL里面普通的select是不加锁的，通过multi-versioning来保证consistent read。 实际上读取的是db里面某个时间点的snapshot。query可以看见query时间点之前提交的transaction的所有改变，但不能看见之后或者未提交的transaction所做的改变。

##Locking Reads

select ....fro update  
select lock in share mode

普通的select 语句不适用于在同一个transaction里面，如果你需要先读取数据然后再create/update/delete数据的情况。 因为别的transaction可能或更新或删除同样的row。因此适用Locking Read保证其他transaction不会改变当前transaction的数据。

##Record，Gap，Next-key Locks

InnoDB 有三种record-level的锁。分别称之为： 
  
* record lock: lock某个index record。如果某个表没有定义index的话，将会使用隐性的聚类索引作为record lock。
* gap lock：lock某些index record的区间或第一个index record之前的区间或最后一个index record之后的区间。
* next-key lock：record lock和gap lock的组合。
InnoDB的行级锁实际上是index-record locks。

##How to cope with Deadlocks

1. show engine innodb status
2. enable innodb\_print\_all\_deadlocks for debug. and disable it after finished debugging.
3. re-issue a transaction，try again
4. 尽量使用小的transaction以减少冲突的概率。
5. 相应操作完成后，尽早提交commit。特别地，不要在未提交的transaction中长久不关闭inactive的session。
6. 在使用locking read的时候，尽量使用较低级的isolation level，如read commited。
7. 在一个transaction中，设计改动多个表，或者同个表的不同的行时，考虑每次执行时的consistent order。比如将多个db的操作存入存储过程中，而不是在不同的地方调用类似的insert,update,delete语句。
8. 良好设计的index。
9. use less locking。
10. 所有的方法都不行的话，只能试试serialization 的isolation level了。


##Application level Lock

<http://dev.mysql.com/doc/refman/5.5/en/miscellaneous-functions.html>

* get_lock(str,timeout): 试图在给定时间内获得锁。若timeout为负，则为infinite timeout. ( 个人理解应该是无限等待，然而实际测试发现不是，如果一个锁一个被其他的connection获取，即使timeout设为负值也是里面返回）
* release_lock(str)：释放锁，必须是锁的拥有者，否则返回0，或者null（如果该锁被被lock的话）
* is_free_lock(str)：是否被锁。free返回1.
* is_used_lock(str)：是否被锁, 被锁的话返回持有锁的connection的identifier。(对应processlist里面的ID）。
http://hedengcheng.com/?p=771