# clickhouse常用语法

## 1、创建表

### 1.1 创建MergeTree表

```
create table saas_cc.obj_track(

obj_id Stirng,

capture_time DateTime,

site_id String

) engine=MergeTree()

order by (obj_id,site_id)
```

会按照order by后面的字段创建一级索引，在这个表数据量很大的时候，通过obj_id查询表数据会很快

### 1.2 创建ReplacingMergeTree表

```
create table saas_cc.site_id_mapping(

site_id String,

site_id_old String,

site_name String`

) engine=ReplacingMergeTree()

order by (site_id,site_id_old)
```

要点：

1、ReplacingMergeTree是一个可以将重复主键数据进行合并的数据库，根据order by后面指定的字段作为主键，当相同的主键数据写入表时，clickhouse可以进行合并

### 1.3 创建远程表

#### 1.3.1 pg数据库同步到ck

create database pg237_ck_pg 

ENGINE=PostgreSQL('59.46.135.237:5432','ck_pg','postgres','Init1234')

注意：如果在pg库创建了新的schema，那么这个远程连接就不可用了

#### 1.3.2 pg数据库表同步到ck

```
CREATE TABLE default.t_imsi_relation_info(

    id String,

	imsi String,

	update_time timestamp

)

ENGINE = PostgreSQL('192.168.14.157:5432', 'skkj_db', 't_imsi_relation_info', 'postgres', 'postgres');
```



#### 1.3.3 mysql库表同步到ck

```
create database mysql_79_test2 

ENGINE=MySQL('10.73.200.79:3306','test2','admin','Init1234')
```



### 1.4 创建物化视图

#### 1.4.1 aggregating物化视图

这一类物化视图，可以进行sum、max、avg计算

如果之前物化视图基本表中已经有历史数据，物化视图不会计算历史数据，只从最新的数据开始计算；如果需要计算历史数据，只能将数据写入到临时表，并且将临时表数据重新写入到正式表中

```
CREATE MATERIALIZED VIEW saas_cc.obj_peer_sum_others_aggregate
(obj_idInt64,peer_obj_idInt64,relation_codeString,partition_colInt8,peer_timesAggregateFunction(sum,Int32),peer_daysAggregateFunction(sum,Int32),peer_date_minAggregateFunction(min,Date),peer_date_maxAggregateFunction(max,Date)
)
ENGINE = AggregatingMergeTree
PARTITION BY partition_col
ORDER BY (obj_id,peer_obj_id)
AS
SELECT
    obj_id,
    peer_obj_id,
    relation_code,
    partition_col,
    sumState(peer_times) AS peer_times,
    sumState(peer_days) AS peer_days,
    minState(peer_date_min) AS peer_date_min,
    maxState(peer_date_max) AS peer_date_max
FROM saas_cc.obj_peer_sum_others_detail_v2
GROUP BY
    obj_id,
    peer_obj_id,
    relation_code,
    partition_col;
```



#### 1.4.2 物化视图查询

在查询物化视图的时候，需要使用merge关键词，因为物化视图地城存的是二进制数据，查询需要使用特殊的语法

```
select obj_id,peer_obj_id,relation_code,sumMerge(peer_times) as peer_times,sumMerge(peer_days) as peer_days,minMerge(peer_date_min) as peer_date_min,
	       maxMerge(peer_date_max) as peer_date_max,partition_col,now()
	from saas_cc.obj_peer_sum_others_aggregate where partition_col=v_partition_num group by obj_id,peer_obj_id,relation_code,partition_col
```



### 1.5 创建TTL表

#### 1.5.1 指定列的TTL

```
CREATE TABLE example_table
(
    d DateTime,
    a Int TTL d + INTERVAL 1 MONTH,
    b Int TTL d + INTERVAL 1 MONTH,
    c String
)
ENGINE = MergeTree
PARTITION BY toYYYYMM(d)
ORDER BY d;

```

#### 1.5.2 指定表的TTL

```
CREATE TABLE example_table
(
    d DateTime,
    a Int
)
ENGINE = MergeTree
PARTITION BY toYYYYMM(d)
ORDER BY d
TTL d + INTERVAL 1 MONTH [DELETE],
    d + INTERVAL 1 WEEK TO VOLUME 'aaa',
    d + INTERVAL 2 WEEK TO DISK 'bbb';

```

说明：

DELETE - 删除过期的行（默认操作）;
TO DISK ‘aaa’ - 将数据片段移动到磁盘 aaa;
TO VOLUME ‘bbb’ - 将数据片段移动到卷 bbb.

### 1.6 创建副本表

```
CREATE TABLE saas_cc.obj_id_mapping on cluster ck_cluster
(obj_id_str String,obj_id Int64,obj_type String
)
ENGINE = ReplicatedReplacingMergeTree('/clickhouse/tables/{shard}/obj_id_mapping','{replica}')
ORDER BY (obj_id_str,obj_type)
SETTINGS index_granularity = 8192;
```

注意：{shard}和{replica}这两个参数都是从集群配置文件中获取到



### 1.7 创建分区表

```
CREATE TABLE saas_cc.vehicle_capture
(
plate_no String,plate_color String,capture_time DateTime
)
ENGINE = MergeTree
partition by toYYYYMM(capture_time)
ORDER BY (plate_no,plate_color);
```

分区字段一般指定整形字段或者日期字段，分区就是将数据分为不同的文件块，便于查询和管理

上面例子中就是获取capture_time的年月来作为分区

## 2、语法

### 2.1 查询

#### 2.1.1 远程查询

```
select * from remote('ip地址',表名,'用户名','密码')

select * from remote('10.73.200.79',saas_cc.obj_track,'default','Init1234')
```



#### 2.1.2 窗口函数查询

1、实现lag() lead()查询

```
select job_id,calc_start_time,
any(calc_start_time) over(partition by job_id order by calc_start_time desc rows between 1 PRECEDING and 1 PRECEDING) as time_before,
any(calc_start_time) over(partition by job_id order by calc_start_time desc rows between 1 FOLLOWING and 1 FOLLOWING) as time_after
from saas_cc.job_log_detail_v2
order by job_id ;
```



需要开启窗口函数，可以在配置文件中设置，也可以在clickhouse-client客户端去临时设置

```
settings allow_experimental_window_functions=1;
```

![image-20220307111114452](\图片\image-20220307111114452.png)

2、低版本实现 rank() over() (array join)

```
select array_site_id,array_type,sxh from (
select groupArray(site_id) as array_site_id,groupArray(site_type_lv1) as array_type 
from saas_cc.site_mapping 
) array join array_site_id,array_type,arrayEnumerate(array_site_id) as sxh
```



![image-20220307112111745](图片\image-20220307112111745.png)

说明：低版本的clickhouse，没有开窗函数，只能先将数据聚合到一个数组里面，并且使用arrayEnumerate函数获取数组每一个元素的位置，再使用array join将数据由行转为列，将数据拆开，然后再进行查询，就获取到数据的排序

### 2.2 更新

1、使用update语法

注意：如果表数据量很大，执行这个操作可能会将数据表损坏

```
alter table saas_cc.obj_track update capture_time='2022-01-01 12:32:13' where obj_id='232323242432'
```

2、利用临时表

create+insert+rename



### 3.3 删除

1、使用delete语法

注意：如果表数据量很大，执行这个操作可能会将数据表损坏

```
alter table saas_cc.obj_track delete where obj_id='123';
```

2、利用临时表

create+insert+rename

3、删除分区

```
alter table saas_cc.obj_track drop partition 20210101
```



### 2.4 合并数据

1、手动合并表数据

将被标记删除的数据、重复的数据进行合并

```
optimize table saas_cc.obj_track final;
```

2、查询最新数据

```
select * from saas_cc.obj_track final;

```

### 2.5 表重命名

```
rename table saas_cc.obj_track to saas_cc.obj_track2
```



## 3、常用函数

### 3.1 数据类型转换

```
SELECT CAST(et, 'Int8') FROM enum_t
```



### 3.2 去重统计

常规语法：

```
count(distinct site_id)
```

高性能语法：

```
groupBitmapMerge(site_id)
```



### 3.3 日期函数

#### 3.3.1 时间字符串转DateTime

toDateTime 可以将所有符合要求的时间字符串，转换成统一的yyyy-mm-dd hh24:mi:ss 格式的时间

toDateTime('2022-01-01 12:32:30')

toDateTime('2022-01-01 12:32:30','Asia/Shanghai')



#### 3.3.2 字符串转换成 DateTime

1、将能识别出的字符串日期转换成DateTime字段类型

```
SELECT parseDateTimeBestEffort('20201010121212') from system.one 
```

2、如果不能转换的，就给成Null

```
parseDateTimeBestEffortOrNull
```

#### 3.3.3 时间差计算

1、计算两个日期相差多少秒

```
select dateDiff('second',toDateTime('2020-07-25 18:24:52'),toDateTime('2020-07-25 18:21:58'))
```

2、计算两个日期相差多少分钟

```
select dateDiff('minute',toDateTime('2020-07-25 18:24:52'),toDateTime('2020-07-25 18:21:58'))
```



### 3.4 字符串相关函数

#### 3.4.1 null值处理

如果数据是null，则给一个默认值，如下，如果col1字段中有为null的值，就默认给ddddd

```
coalesce(col1,'ddddd') 
```

#### 3.4.2 取MD5值

```
halfMD5(obj_id)
```

#### 3.4.3 将int转换成String

```
toString(obj_id)
```



### 3.5 高级函数

#### 3.5.1 groupBitmap

字段类型必须是整形数据，低版本字段数据类型必须是有符号的整形数据，例如UInt8、UInt16等等

groupBitmap是一种高效的求count(distinct )统计的函数

```
select groupBitmap(site_id_int) from saas_cc.site_mapping
```

#### 3.5.3 rowNumberAllBlocks()（获取行号）

这个值是固定不变的，通常可以用来给数据分片

```
select *,rowNumberAllBlocks() from saas_cc.relation_calc_sum_hugeGraph
```

### 3.5.4 数组函数

1、返回数组不重复元素的个数

```
arrayUniq
```

2、数组中元素进行去重返回

```
arrayDistinct
```

3、多个数组拼接

```
arrayConcat
```

4、将一列多个值，转换成一个数组，是聚合函数

```
不去重：groupArray(site_id)
去重：groupUniqArray(site_id_int) 
```

将多行数据聚合成一个数组

```
select obj_type,groupArray(obj_id) from saas_cc.obj_id_mapping group by obj_type;
```

5、数组元素排序

```
升序：arraySort
降序：arrayReverseSort
```



## 4、系统表

### 4.1 合并任务表

查看系统后台合并任务 

```
SELECT * FROM system.metrics;
```

### 4.2 查看集群节点

```
select * from system.clusters
```

### 4.3 查看表的压缩率

```
select 
table,sum(rows),formatReadableSize(sum(data_uncompressed_bytes)) as uncompressed,
       formatReadableSize(sum(data_compressed_bytes)) as compressed,
       round((sum(data_compressed_bytes)/sum(data_uncompressed_bytes))*100,0) as privat
from system.parts where database='zdsk' and table='imsi_capture' group by table;
```



## 5、数据类型

### 5.1 字符串

String

### 5.2 整形

有符号类型（-2n-1~2n-1-1）：

Int8/Int16/Int32/Int64

无符号类型（0~2n-1）：

UInt8/UInt16/Uint32/UInt64

布尔类型用UInt8：限制为0或1

### 5.3 浮点型

Float32->float

Float64->double

### 5.4 日期

Date

日期格式：2022-04-02

DateTime

日期格式：2022-04-01 12:00:00



### 5.5 数组

Array(T)

Array(String)

Array(Int)

#### 5.5.1 表结构

```
create table default.array_table_test(
id String,
color Array(String)
)engine=MergeTree() order by id
```

#### 5.5.2 数组函数

1、返回数组不重复元素的个数

arrayUniq

2、数组中元素进行去重返回

arrayDistinct

3、多个数组拼接

arrayConcat

4、将一列多个值，转换成一个数组，是聚合函数

不去重：groupArray(site_id)

去重：groupUniqArray(site_id_int) 

5、数组元素排序

升序：arraySort

降序：arrayReverseSort



## 6、数据的导入和导出

### 6.1 数据导入

将csv文件导入到数据表中（如果不指定分隔符，分隔符默认就是Tab分隔）

```
csv格式数据导入：
clickhouse-client --user default --password Init1234 --query "insert into saas_cc.obj_track FROMAT CSV" < data.csv
Tab分隔符数据导入：
clickhouse-client --user default --password Init1234 --query "insert into saas_cc.obj_track FORMAT TabSeparated“ < data.txt
```

### 6.2 数据导出

将数据导出，导出一个csv文件

```
不指定导出数据格式，默认是Tab分隔符
clickhouse-client --user default --password Init1234 --query "select * from saas_cc.obj_track" > data.txt
指定导出数据格式为csv：
clickhouse-client --user default --password Init1234 --query "select * from saas_cc.obj_track FORMAT CSV" > data.csv
```



## 7、clickhouse部署

### 7.1 单节点部署

#### 7.1.1 ubuntu安装

1）查看已经安装的clickhouse软件包

```
dpkg --list |grep clickhouse
```

2）软件安装

```
dpkg -P clickhouse-common-static
dpkg -P clickhouse-client
dpkg -P clickhouse-server
```

注意：安装server的时候，需要设置default用户的密码

3）给数据目录 添加权限

```
chmod -R /data
```

修改系统的文件打开数：**ulimit -n 124000**

4）修改配置文件

a、修改 /etc/clickhouse-server/users.xml

修改用户能使用的最大内存以及用户的密码

```
<max_memory_usage>4000000000000</max_memory_usage>
<password></password>
```

b、修改/etc/clickhouse-server/config.xml

```
<listen_host>::</listen_host>
<path>/data/clickhouse/</path>
<tmp_path>/data/clickhouse/tmp/</tmp_path>
<timezone>Asia/Shanghai</timezone>
<format_schema_path>/data/clickhouse/format_schemas/</format_schema_path>
<user_files_path>/data/clickhouse/user_files/</user_files_path>
<access_control_path>/data/clickhouse/access/</access_control_path>
```



ip监听配置，设置为 ::表示运行所有客户端ip访问clickhouse服务

clickhouse数据保存目录

clickhouse临时数据保存目录

clickhouse时区

format_schema保存路径

用户文件保存路径

配置文件路径

#### 7.1.2 centos安装

1）查看clickhouse软件包

```
rpm -qa |grep clickhouse
```



2）安装rpm包

```
rpm -ivh clickhouse-common-static
rpm -ivh clickhouse-client
rpm -ivh clickhouse-server
```



### 7.2 集群部署

集群配置，需要依赖zookeeper，需要创建一个配置文件metrikal.xml文件

在配置文件中，需要配置集群的信息

![image-20220307133707342](图片\image-20220307133707342.png)



## 8、实践经验（数据计算部分）

### 8.1 clickhouse离线计算

#### 8.1.1 离线计算方式

1）编写shell脚本的方式，部分逻辑写在shell脚本中，因为shell脚本中，可以执行clickhouse-client客户端命令，很方便能获取数据库表信息

2）可以使用azkaban获取linux自带的crontab进行shell 脚本的任务调度，来定时进行离线计算

#### 8.1.1 离线计算优化总结

1）在clickhouse离线计算中，最好定义整形数据字段，可以提高计算速度

2）在数据计算过程中，如果查询的表数据量太大，可以将需要计算的数据写入到临时表，再用临时表进行join

3）数据全量计算，如果出现OOM或者执行时间特别长，可以将数据进行分片，一个分片一个分片进行计算，或者将多个分片进行并行计算

4）在设计表的时候，要注意分区字段的设置，分区不能太多也不能太少



## 9、dbeaver客户端工具

### 9.1 客户端连接

![image-20220402114647399](\图片\image-20220402114647399.png)

![image-20220402115101893](\图片\image-20220402115101893.png)



### 9.2 外部驱动配置

1、直接下载整个https://github.com/moshowgame/dbeaver-driver-all   ，然后拷贝到 C:\Users\mosho\AppData\Roaming\DBeaverData\drivers   ，或者再首选项>连接>驱动>驱动位置进行设置。

![img](图片\clipboard.png)



2、在驱动设置，添加驱动包的文件夹，选到clickhouse那一个文件夹目录

![image-20220402115548166](\图片\image-20220402115548166.png)



### **9.3 客户端工具参数配置**

**1、超时时间控制 timeout**

有时候sql执行时间较长时，需要延长超时时间

右键创建的连接->编辑连接

![img](图片\clipboard2.png)

![img](D:\有道云\本地内容保存\qq956198707365E2F59390AB678A5DCF1E\3a4d1e07f2434d249f92cee9aaec404a\clipboard.png)

socket_timeout=600000   超时时间设置成600s

