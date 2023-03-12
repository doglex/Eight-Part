[TOC]

## Hive 
+ Hive表的管理借助元数据库（derby或者mysql）
+ 存储借助HDFS
+ 查询借助HQL转成MapReduce
+ 锁借助Zookeeper

## 数据类型
+ 基本类型：TINYINT,SMALLINT,BIGINT,BOOLEAN,FLOAT,DOUBLE,TIMESTAMP,BINARY
INT,STRING
+ 集合类型： STRUCT,MAP,ARRAY

## 分隔符
+ 字段分割默认是\001
+ 用Hive Streaming 时是 \t 
+ 记录(行)间是 \n 

## Schema On Read 读时模式
+ 与传统数据库不同，以textfile格式写入HDFS时不进行类型检查。读出时类型检查，不符合设为null空。
+ 方便存储，方便读的时候更改类型，方便忽略错误
+ 使用parquet格式时不支持读时模式

## 数据插入
+ 不支持方便的行级插入，默认不支持事务(其实是可以的)。
+ 有 Insert Overwrite(覆盖目录) 和 Insert Into(追加文件)
> Insert overwrite table1  (partition date='2023-03-12) select * ...
+ Hive数据库本质是目录或者命名空间。分区是符合条件的文件夹，子文件夹，比如.../event_day=20170606/event_name=‘on’/…,其他字段在这种文件夹内，可以节约这个分区字段的存储开销，而且查找分区时速度快，也可以整块删除分区。

## 表操作示例
```
CREATE DATABASE x； — - 为每个数据库创建一个目录，在hive.metastore.warehouse.dir的顶层目录下的x.db
CREATE DATABASE  IF NOT EXISTS x； — - 对于连续程序可以忽略错误
CERATE DATABASE LOCATION ‘/my/location’ COMMENT ‘replace default storage’; — 修改dfs中的目录
SCHEMA 可以替代TABLE关键字
SHOW DATABASES LIKE ‘h.*’;
USE namespace xs; —使用为当前空间
use x； —使用为当前数据库
show tables；
drop database if exists x cascade； — cascade 表示迭代删除其中的表，restrict 表示要检查其中是否还有表。
alter database x set DBPROPERTIES (‘editor’=‘xs’) — 其他元信息是不能修改的，仅仅这个描述信息可以修改
```
impala（与hive共用元数据） 操作
```
Refresh xxx;
Invalidate xxx;
Invalidata metadata;
```
## 建表
默认创建的是内部表，hive拥有完全管理权，但是hdfs dfs工具也是可以删除表的。也可以创建外部表，使用EXTERNAL关键字，指定LOCATION。当删除外部表时，不会删除数据，只是删除derby（单线程）或者mysql（多线程）里的元信息。
对于hive管理的表，可以复制表结构

## 分区 Partition 
+ 比如以日期建立分区
+ where语句对于分区字段来说就是文件目录过滤器，依赖是根据这个目录是否存在
+ 容错：hive不关心分区是否存在，查询不到就不过是fetch到0条而已。
+ 当where里没有指定分区字段的值时，mapreduce会全表扫描，非常消耗性能
```
set hive.mapred.mode = strict ; —-严格要求指定分区字段，而nonstrict则不需要
show partitions table name；
alter table table0 add partition (year=2012,month=1,day=2)  — 添加分区
```

## 特殊操作
+ explode  将集合类型生成到多行
+ inline   将Struct类型提取出来插入表中
+ json_tuple,parse_url_tuple 获得tuple
+ stack(n,col1,,,,colM) 将M列转换为N行，每行有M/N个字段，n要求常数
+ get_json_object(x, '$.c1'') 提取json中字段
> 注意：get_json_object函数在impala2.8以前版本，和group同时使用时需要加concat，否则得到错误结果

> 注意：float的0.2与double的0.2比较结果更大，而不是相等。需要强转进行比较

## 联表Join
+ 只支持SQL中的等值连接。
1.INNER JOIN 当两个表中都存在的数据保留
```
select a.ymd,a.price_close,b.price_close
from stocks a JOIN stocks b ON a.ymd = b.ymd
where a.symbol = ‘AAPL’ and b.symbol =‘IBM’
```
— ON是连接条件，where可以限制各个表中的数据，同时可以使用表别名。先JOIN，后Where过滤。如果不清楚顺序，应该用嵌套查询。
— 这里使用同一个表，是自连接。
— ON的条件是等值连接，mapreduce中难以实现非等值，标准SQL是支持非等值的，Pig工具支持交叉生产
— ON中不支持OR语句
Left semi JOIN，优化了的内连接，查询结果中只需要左表的值。
Hive不支持右半开连接。

2.多表连接
```
select a.ymd,a.price_close,b.price_close，c.price_close
from stocks a JOIN stocks b ON a.ymd = b.ymd
              JOIN stocks c ON a.ymd = c.ymd
where a.symbol = ‘AAPL’ and b.symbol =‘IBM’
```
+ MR的顺序先连接a，b；再将结果与c连接。从左至右连接。
+ 优化1：ON键连续连接，HIVE尝试将前面的表缓存起来，后扫描后面的表。因此，用户需要让表的大小从小到大从左至右排列。
+ 优化2：『标记』告诉查询优化器哪张是大表，作为驱动表。
select /*+STREAMTABLE (s) */ s.ymd,s.symbol,sprice_close,d.divided
from stocks s JOIN dividends d ON s.ymd = d.ymd AND symbol = d.symbol 
where s.symbol = ‘AAPL’;
+ 优化3：map-side JOIN

4.笛卡尔积。N行xM行

5.map-side JOIN
+ 可以在map的时候将小表放入内存，像查字典一样，从而减少reduce。
```
set hive.auto.convert.join = true;
set hive.mapjoin.smalltable.fileze = 25000000;
SELECT /* MAPJOIN (d) */ s.ymd,s.symbol,s.price_close,d.dividend
from stocks s join dividends on s.ymd = d.ymd and s.symbol = d.symbol
where s.symbol = ‘AAPL’;
```

+  Hive0.7以后可以不指定mapjoin的表。
+ 如果数据是分桶的，则大表也可以优化。简单的说，必须按on键分桶，且一张表的桶数是另一张表的若干倍。
set hive.optimize.bucketmapJOIN =true;
+ hive对于右外连接和全外连接不支持这个优化。
> map-side join 就是把小表形成哈希表，在map时就查哈希表联好，甚至计算好；reduce side join，就是都要在reducer里进行关联；map-side join 性能更好。

