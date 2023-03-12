[TOC]

## Hive 
+ Hive表的管理借助元数据库（derby或者mysql）
+ 存储借助HDFS
+ 查询借助HQL转成MapReduce任务。Map Reduce就是分组聚合，类似groupby-apply 
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


## 排序
1.Order By和 Sort BY
+ 尽量不用order by。ORDER BY 是全局排序，所有数据需要通过一个reducer，当hive.mapred.mode 设置为strict的时候严格要求加LIMIT
+ SORT BY 是局部排序，各个reducer各自排序，不保证全局有序
+ 都可以使用升序asc，降序desc，缺省是asc

2.含有SORT BY的DISTRUBUTE BY
+ DISTRIBUTE BY 可以控制map的输出在reducer中是如何划分的，如果不指定，默认是按哈希值进行均匀划分。可以手工指定键，以保证在这个键上的sort by是有序的。
```
select s.ymd,s.symbol,s.price_close 
from stocks s 
distribute by s.symbol
sort by s.symbol ASC, s.ymd asc
```
+ 可以保证在各个s.symbol上是有序的
+ distribute by 一定要在 sort by 之前

3.CLUSTER BY 是 SORT BY+DISTRUBUTE BY的简写，当两者的键一致时
```
select s.ymd,s.symbol,s.price_close
from stocks s 
cluster by s.symbol;
```

>  使用Hive Streming时，可以指定cluster by，从而将需要的主键划到同一个桶中进行计算，而不会分散到不同的桶里 

##  视图
1.视图是一个逻辑上的表，并不存储数据，只是一个表的象征。
+ 可以先执行视图，来简化子查询。
+ 实际上是制定了一个逻辑流程。
+ 对于查询效率没有实质上的提升。
+ hive没有物化视图。
+ 视图是只读的，只允许修改TABLEPROPETIES的描述信息。

原语句
```
from (
select * from people join cart 
on (cart.people_id = people.id)
where firstname = ‘john'
) a
select a.lastname where a.id = 3;
```
以视图修改
```
create view shorter_join as 
select * from people join cart
on (cart.people_id = people.id)
where firstname = ‘john’;
select lastname from shorter_join where id = 3;
```

2.通过视图限制访问 create view safe_table as select firstname, lastname from userinfo;

3.通过视图从 map、struct、array中取出一部分数据，从而把数据压平。

4.删除视图 drop view if exists shipments；

> 可以用with语句定义多个步骤

## Hive索引
```
1.hive支持的索引有限。
可以帮助裁剪数据减少mapreduce压力。但对有些查询是没用的。
需要消耗额外的存储，创建索引本事也消耗计算资源。
可以用EXPLAIN 语句来查看某个语句是否用了索引。
2.创建索引
除了S3外，对外部表和视图都可以建立索引。
create index employees_index 
on table employees (country)
as ‘org.apache.hadoop.hive.ql.index.compact.CompactIndexHandler’  — 也可以是BITMAP类型
with deferred rebuild 
IDXPROPERTIES (‘creator’=‘me’.’at’=’some time’)
in table employee_index_table
partition by (country, name);
3.重建索引
数据改变时索引无法自动触发重新索引。
alter index employees_index
on table employees
partition (country = ‘cn’)
rebuild;
4.显示索引
show formatted index on employees；
5.删除索引
表或者分区删除时，相应的索引也会被删除。hive用户不允许在删除表之前删除索引。
drop index if exists employees_index on table employee；
```

## 最佳实践
1. 应尽量避免反模式
+ 按天划分的表 supply_2017_01_06 这样表的数量增长是很快的，是一种反模式。
+ 需要改成一张表的各个分区，用event_day作为分区字段。

2. 分区
+ 分区很有用，减小了全盘扫描。
+ 分区应该是一些文件的合并，每个分区下的文件应该足够大。如果分区数过多，就是文件夹数量过多。而HDFS NameNode需要保存元信息，保存不下了。

3. hive不保证唯一键和标准化，数据可能是重复的，是为了保证TB级和PB级的计算。
> 没有主键的概念

4. 同一个数据的多次处理，只需要一轮扫描
``` sql
低效率
insert overwrite table sales
select * from history where action = ‘purchase’;
insert overwrite table credits 
select * from history where action = ‘returned’; 
高效率
from history 
insert overwrite sales select * where action = ‘purchase’;
insert overwrite credits select * wherer action =‘returned’;
```

5. 设计中间表，按天划分分区，减少很多重复工作。

6. 总是使用压缩。MapReduce任务是偏于IO密集型的，有多余的CPU用来压缩解压缩。但是对于CPU开销大的机器学习就不要用压缩了。

7. 记得用使用EXPLAIN [EXTENDED] 解释执行过程，从而调优

8. JOIN 优化。使用left join，且将小表放前面，可能会调用 map-side join

9. 并行执行。
```
set hive.exec.parallel = true;
```

10.JVM重用
```
set mapred.job.reuse.jvm.num.tasks = 10;
```

11.配置动态分区
```
hive.exec.dynamic.partition.mode
hive.exec.max.dynamic.partitions
hive.exec.max.dynamic.partitions.pernode
dfs.datanode.max.xcievers
```

12. 推测执行:侦测将执行慢的TaskTracker加入黑名单
```
mapred.map.tasks.speculative.executor = true ;
mapred.reduce.tasks.speculative.executor = true ;
```

13.虚拟列
```
set hive.exec.rowoffset = true;
```

14.开启压缩,减少传输
```
set hive.exec.compress.intermediate = true;
mapred.map.output.compression.codec;
hive.exec.compress.output
mapred.output.compression.codec
```

15.设置reducer数量，也决定输出文件个数，个数也影响到下游任务的task数量
```
hive.exec.reducers.bytes.per.reducer=256000000;
hive.exec.reducers.max=1009;
et mapreduce.job.reduces = 15;
```

16.map输入前合并小文件


## Hive Streaming
+ 建议使用通用的TRANSFORM
+ Streaming为外部进程开启了一个IO管道，数据传入标准输入，之后从标准输出返回到Streaming API job
+ TRANSFORM用的是UTF-8
1.简单变换
```
select transform(a,b) using ‘/bin/cat’ as newA, newB from default.a; —恒等变换
select transform(a,b) using ‘/bin/cat’ as (newA INT, newB DOUBLE) from default.a; —改变类型
select transform(a,b) using ‘/bin/cut -f1’ as newA from a; —投影
select transform(a,b) using ‘/bin/sed s/4/10/‘ as newA,newB from a; —一部分不输出到标准输出的话，行数是可以更改的
```

2.分布式缓存
```
将文件或者依赖的数据分发到每个节点，让每个节点都能执行这个文件
add file ${env:HOME}/prog_hive/ctof.sh
select transform (col1) using ‘ctof.sh’ as convert from a;
```

3.使用其他程序
```
只需要控制好输入输出即可
每行使用tab进行分隔
一行产生多行或者减少行只需要控制输出行即可，python的print是自带换行符的
select transform (col1) using ‘python ctof.py’ as convert from a;
select transform (col1) using ‘perl ctof.pl’ as convert from a;
自定义python解释器
使用add cachearchive的方法把tar.gz添加到分布式缓存，Hive会自动解压压缩包
解压后的目录名是和压缩包名称一样的：
./<压缩包名字>/<压缩的相对路径>/bin/python
add cachearchive ${env:my_workbench}/share/python2.7.tar.gz;
transform中使用：
using 'python2.7.tar.gz/bin/python my.py' 
```

4.将Streaming用于聚合

比如求和
```
#!/usr/bin/perl
my $sum=0;
while (<STDIN>){
my $line = $_;
chomp($line);
$sum=${sum}+${line};
}
print $sum;
```
```
add file ${env:HOME}/aggregate.pl;
select transform (number)
using ‘perl aggregate.pl’ as total from sum;
```

5.利用CLUSTER BY进行wordcount
+ CLUSTER BY 可以保证同一个key在同一个reducer中，且数据有序，但是一个cluster可能有多个key
+ 相当于集成了distribute by 和 sort by
+ 注意UDF分发到各个机器上，不好利用全局字典
```
import sys
for line in sys.stdin:
    words = line.strip().split()
    for word in words:
        print “%s\t1”%(word.lower())

import sys
last_key, last_count = None,0 
for line in sys.stdin:
    key, count = line.strip().split(“\t”)
    if last_key and last_key ！= key:
        print “%s\t%t%d” (last_key,last_count)
    else:
        last_key = key
        last_count += int(count)
if last_key:
    print “%s\t%t%d” (last_key,last_count)
```
```

CREATE TABLE docs (line STRING);
CREATE TABLE word_count (word STRING, count INT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ‘\t’;

FROM (
FROM docs
SELECT TRANSFORM (line) USING ‘${env:HOME}/mapper.py’
AS word, count 
CLUSTER BY word ) wc
INSERT OVERWRITE TABLE word_count
SELECT TRANSFORM (wc.word, wc.count) USING ‘${env:HOME}/reducer.py’
AS word,count; )
```

