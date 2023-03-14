## 架构
+ 逻辑架构：客户端--连接器(线程)--查询缓存--解析器--优化器--存储引擎
+ 每个客户端连接在服务器进程中有一个线程，该线程在CPU中轮流得到执行
+ 读锁(Read Lock)是共享(shared)的、写锁(Write Lock)是排他(exclusive)的。加锁也需要额外资源
+ 有表锁(table lock)、行级锁(row lock)

## 事务(Transaction，ACID):Atomicity、Consistency、Isolation、Durability
+ 原子性：不可分割，要么一起执行，要么一起不执行
+ 一致性：数据库总是从一个一致性状态到另一个一致性状态
+ 隔离性：一个事务提交前，其他事务不可见
+ 持久性：一旦事务提交，所做的更改永久保存在数据库中

## 隔离级别
+ 未提交读：即使没提交，也被别的事务看到。
+ 提交读：很多数据库默认。
+ 可重复读：阻止脏读。Mysql默认，Mysql的InnoDB也是可以(MVCC)防止幻读的
+ 可串行化：最高隔离级别

## 死锁
+ 两个事务统一资源相互占用，并循环请求对方资源
+ InnoDB有死锁检测和死锁超时机制

## 事务日志
预写式日志，Write-Ahead Logging 

## InnoDB引擎
+ 数据存储在表空间tablespace 
+ 基于聚簇索引建立
+ 用MVCC多版本控制来支持多事物的高并发
+ 有优化，可预测预读等

## 其他引擎
+ MyISAM
+ Archive
+ Blackhole
+ CSV
+ Federated
+ Memory
+ Merge 
+ NDB
+ XtraDB(OLTP)
+ InfoBright(列存储)