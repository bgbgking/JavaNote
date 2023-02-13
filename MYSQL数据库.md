# MYSQL

## SQL

### DDL(Data Definition Language)

#### 数据库

##### 查询所有数据库

show databases;

##### 查询当前数据库

select database();

##### 创建数据库

create database [if not exists] 数据库名 [default charset 字符集] [collate 排序规则];

##### 删除数据库

drop database [if exists] 数据库名;

##### 使用数据库

use 数据库名;

#### 表

##### 查询当前数据库所有表

show tables;

##### 查询表结构

desc 表名;

##### 查询指定表的建表语句

show create table 表名;

##### 创建表

create table 表名(

​	字段1 字段1类型 [comment 字段1注释],

​	......

​	字段1 字段1类型 [comment 字段1注释]

)[comment 表注释];

##### 添加字段

alter table 表名 add 字段名 字段类型（长度）[comment 注释] [约束];

##### 修改数据类型

alter table 表名 modify 字段名 新数据类型（长度）;

##### 修改字段名和字段类型

alter table 表名 change 旧字段名 新字段名 类型（长度） [comment 注释] [约束];

##### 删除字段

alter table 表名 drop 字段名;

##### 修改表名

alter table 表名 rename to 新表名;

##### 删除表

drop table [if exists] 表名;

##### 删除指定表并重新创建该表

truncate table 表名;

### DML(Data Manipulation Language)

#### 添加数据

##### 给指定字段添加数据

insert into 表名 (字段名1，字段名2，...) values (值1，值2);

##### 给全部字段添加数据

insert into 表名 values (值1，值2);

##### 批量添加数据

insert into 表名 (字段名1，字段名2，...) values (值1，值2)，(值1，值2)，(值1，值2);

insert into 表名 values (值1，值2)，(值1，值2)，(值1，值2);

##### 注意

字符串和日期型的数据用引号括起来

#### 修改数据

update 表名 set 字段名1 = 值1，字段名2 = 值2，...[where 条件];

##### 注意

update语句条件可以有，也可以没有，若没有，则修改表中所有数据

#### 删除数据

delete from 表名 [where 条件];

##### 注意

delete语句条件可以有，也可以没有，若没有，则删除表中所有数据

delete语句不能删除某一字段的值

### DQL(Data Query Language )

#### 语法

select 字段列表 from 表名列表 where 条件列表 group by 分组字段列表 having 分组后条件列表 order by 排序字段列表 limit 分页参数

#### 基本查询

##### 查询多个字段

select 字段1，字段2，... from 表名;

select from 表名;

##### 设置别名

select 字段1 [as 别名1],字段2[as 别名2] ... from 表名;

##### 去除重复记录

select distinct 字段列表 from 表名;

#### 条件查询

##### 运算符

<> 不等于

between...and... 在某个范围内

in(...) 在in之后的列表中的值

like 占位符 模糊匹配（_ 匹配单个字符，%匹配多个字符）

is null 是null值

and 或 && 并且

or 或 || 或者

not 或 ! 非，不是

#### 聚合函数

##### 常见聚合函数

count	统计数量

max	最大值

min	最小值

avg	平均值

sum	求和

##### 语法

select 聚合函数（字段列表） from 表名;

##### 注意

null值不参与聚合函数运算

#### 分组查询

##### 语法

select 字段列表 from 表名 [where 条件] group by 分组字段名 [having 分组后过滤条件];

##### where和having的区别

where是分组之前进行过滤，不满足where，不参与分组；having是对分组之后的结果进行过滤。

where不可以对聚合函数进行判断，having可以。

#### 排序查询

##### 语法

select 字段列表 from 表名 order by 字段1 排序方式1，字段2 排序方式2；

#### 排序方式

ASC：升序（默认）

DESC：降序

##### 注意

多字段排序时，前面的字段相同时才会对后面的字段进行排序

#### 分页查询

##### 语法

select 字段列表 from 表名 limit 起始索引，查询记录数；

##### 注意

分页查询是数据库的方言，不同的数据库有不同的实现

#### 执行顺序

from->where->group by->having->select->order by->limit

### DCL(Data Control Language)

#### 管理用户

##### 查询用户

use mysql；

select * from user；

##### 创建用户

create user ‘用户名’@‘主机名’ identified by ‘密码’；

##### 修改用户密码

alter user ‘用户名’@‘主机名’ identified with mysql_native_password by '新密码';

##### 删除用户

drop user ‘用户名’@‘主机名’；

##### 注意

主机名可以使用%统配

#### 权限

##### 常用权限

all，all privileges	所有权限

select	查询数据

insert	插入数据

update	修改数据

delete	删除数据

alter	修改表

drop	删除数据库/表/视图

create	创建数据库/表

##### 查询权限

show grants for ‘用户名’@‘主机名’；

##### 授予权限

grant 权限列表 on 数据库名.表名 to ‘用户名’@‘主机名’；

##### 撤销权限

revoke 权限列表 on 数据库名.表名 from ‘用户名’@‘主机名’；

## 函数

### 字符串函数

concat(S1，S2，...Sn)	字符串拼接

lower（str）	字符串转为小写

upper（str）	字符串转为大写

lpad（str，n，pad）	左填充，用字符串pad在str左边进行填充，达到n个字符串长度

rpad（str，n，pad）	右填充，用字符串pad在str右边进行填充，达到n个字符串长度

trim（str）	去掉字符串头尾空格

substring（str，start，len）	返回字符串从start开始的len长度的字符串，索引从1开始

### 数值函数

ceil（x）	向上取整

floor（x）	向下取整

mod（x，y）	返回x%y

rand（）	返回0~1内的随机数

round（x，y）	求x四舍五入的值，保留y位小数

### 日期函数

curdate（）	返回当前日期

curtime（）	返回当前时间

now（）	返回当前日期和时间

year（date）	获取指定date的年份

month（date）	获取指定date的月份

day（date）	获取指定date的日期

date_add（date，interval expr type）	返回一个日期/时间值加上一个时间间隔expr后的时间值

datediff（date1，date2）	返回起始时间date1和date2之间的天数（date1 - date2）

### 流程函数

if（value，t，f）	如果value为true，则返回t，否则返回f

ifnull（value1，value2）	如果value1不为空，返回value1，否则返回value2

case when [val1] then [res1] ... else [default] end	如果val1为true，返回res1，.... 否则返回default默认值

case [expr] when [val1] then [res1] ... else [default] end	如果expr值为val1，返回res1，... 否则返回default默认值

## 约束

### 分类

#### 非空约束

限制该字段的数据不能为null	not null

#### 唯一约束

保证该字段的所有数据都是唯一的、不重复的	unique

#### 主键约束

主键是一行数据的唯一标识，要求非空且唯一	primary key

#### 默认约束

保存数据时，若未指定该字段的值，则采用默认值	default

#### 检查约束（8.0.16版本之后)

保证字段值满足某一个条件	check

#### 外键约束

用来让两张表的数据建立连接，保证数据的完整性和一致性	foreign key

##### 添加外键

create table 表名（

​	字段名 数据类型，

​	...

​	[constraint] [外键名称] foreign key （外键字段名） references 主表（主表列名）

）；

alter table 表名 add constraint 外键名称 foreign key（外键字段名） references 主表（主表列名）；

##### 删除外键

alter table 表名 drop foreign key 外键名称；

## 多表查询

### 多表关系

一对一

一对多

多对多

### 内连接(查询两张表交集)

#### 隐式内连接

select 字段列表 from 表1，表2 where 条件...;

#### 显式内连接

select 字段列表 from 表1 [inner] join 表2 on 连接条件...;

### 外连接

#### 左外连接(左表和交集)

select 字段列表 from 表1 left join 表2 on 条件...；

#### 右外连接(右表和交集)

select 字段列表 from 表1 right join 表2 on 条件...；

### 自连接(可为内连接，可为外连接)

select 字段列表 from 表A 别名A join 表B 别名B on 条件...；

### 联合查询

select 字段列表 from 表A ...

union [all]

select 字段列表 from 表B ...;

#### 注意

联合是指表B的数据直接在表A的下方

表的列数必须保持一致，字段类型也需保持一致

若没有all，表示直接联合，若有，则联合后去重

### 子查询

select * from t1 where column1 = （select column1 from t2）；

子查询外部的语句可以是增删改查任何一个

子查询可出现在where、from、select之后

#### 标量子查询

返回结果是单个值（单行单列）

#### 列子查询

返回结果为一列

#### 行子查询

返回结果是一行

#### 表子查询

返回结果是多行多列

## 事务

### 查看当前事务提交方式

select @@autocommit；

若为1，自动提交

若为0，手动提交

### 设置事务提交方式

set @@autocommit = 0/1；

### 开启事务

start transaction 或 begin；

### 提交事务

commit；

### 回滚事务

rollback；

### 四大特性

原子性	事务是不可分割的最小单元，要不全成功，要不全失败

一致性	事务完成时，所有数据必须保持一致状态

隔离性	数据库系统提供隔离机制，保证事务不受外部并发操作影响的独立环境下运行

持久性	事务一旦提交或回滚，数据库中数据的改变就是永久的 

### 并发事务问题

脏读	事务未提交就读到

不可重复读	事务先后读数据，两次读的数据不相同

幻读	事务中查没有数据，操作又有数据

### 事务隔离级别

read uncommitted

read committed

repeatable read（mysql默认）

  serializable

#### 查看事务隔离级别

select @@transaction_isolation；

#### 设置事务隔离级别

set [session|globle] transaction isolation level 隔离级别

# 进阶

## 存储引擎

### 语法

create table 表名（

​	...

)engine = innodb [comment 注释]；

### 查看当前数据库支持的存储引擎

show engines；

## 索引

### 语法

#### 创建索引

create 【unique|fulltext】 index 索引名称 on 表名（索引列名）；

#### 查看索引

show index from 表名；

#### 删除索引

drop index 索引名 on 表名；

### 索引使用

#### 最左前缀法则

查询从索引的最左列开始，并且不跳过索引中的列，若跳过某一列，索引将部分失效。

#### 索引列运算

在索引列上进行运算，索引将失效

#### 字符串类型加引号

字符串类型字段使用时，不加引号，索引将失效

#### 模糊查询

尾部模糊匹配，索引不会失效。如果是头部，则失效。

#### or连接条件

若or前字段有索引，后字段无索引，则不适用索引。

#### mysql评估

若mysql认为全表查询比使用索引快，则不适用索引。

#### sql提示

在from 表名之后加入人为提示达到优化操作目的

use index；

ignore index；

force index；

#### 前缀索引

当字段类型为字符串时，有时需要索引很长的字符串，浪费磁盘。可将字符串一部分前缀建立索引。

create index 索引名 on 表名（字段名（n））；		//n表示前n个字符 

## 性能分析

### sql执行频率

show global status like ‘Com_______';		//七个下滑线

### 慢索引

#### 查看慢索引

show variables like 'slow_query_log';

#### 开启慢索引日志开关

在mysql配置文件（/etc/my.cnf）中配置

slow_query_log=1

配置完毕后需重启服务器

#### 设置慢日志时间

long_query_time=2			//此处设置为两秒

#### 显示实时慢日志

tail -f localhost-slow.log 

### profile详情

#### 查看当前mysql是否支持profile操作

select @@have_profiling;

#### 开启profiling开关

set profiling = 1；

#### 查看每一条sql耗时基本情况

show profiles；

#### 查看指定id的sql语句各个阶段的耗时情况

show profile for 指定id

#### 查看指定id的sql语句的cpu使用情况

show profile cpu for 指定id；

### explain执行计划

在任意select语句之前加入关键字explain或desc

## sql优化

### 插入优化

#### 批量插入（使用mysql自带load方式）

##### 连接数据库

mysql --local-infile -u root -p

##### 查看是否开启load方式

select @@local_infile; 

##### 开启load方式

set global local_infile = 1；

##### 加载数据

load data local infile ‘/路径（root）/文件名’ into table 表名 fields terminated by ‘，‘ lines terminated by ’\n'；

//数据格式：列按逗号分开，行按换行分开

#### 一条sql语句拼接多个value

### 主键优化

主键长度尽量短

顺序插入

### order by优化

using index：直接通过索引返回数据，性能高

using filesort：需要将返回的结果在排序缓冲区

### limit优化

覆盖查询+子查询

### count优化

性能：count（字段）<count（主键） <count（1）约等于count（*）