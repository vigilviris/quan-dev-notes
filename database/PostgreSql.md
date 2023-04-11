

###  1、字符函数

#### 长度/字节长度

```sql
-- 一个汉字 = 24 bit
-- 一个字符 = 8 bit
SELECT char_length('中文'),bit_length('中文'),
       char_length('abcdefg1234567a'), bit_length('abcdefg1234567a');
-- 2   	48    (2*24)
-- 15  	120    (1*8)
```

#### 下标/位置

```sql
-- 起始下标是1
SELECT position('cd' in 'abcdefg1234567a');
-- 3
```

#### 字符替换

```sql
-- 将下标区间的字符进行替换
SELECT overlay('123456789' placing 'xxxxxx' from 2 for 4);
-- 1xxxxxx6789

-- 字符替换
SELECT replace('aab-abb-ab-aabb', 'ab', 'XY');
-- aXY-XYb-XY-aXYb

-- 翻译
SELECT translate('aab-abb-ab-aabb', 'ab', 'XY');
-- XXY-XYY-XY-XXYY
```

#### 字符截取

```sql
-- substring和substr用法一致,
-- substring(x,y)：截取x的第y位之后的所有字符
-- substring(x,y,z)：截取x的第y位之后z个字符
SELECT substring('aaa123456bbb', 4),
       substring('aaa123456bbb', 4, 6),
       substr('aaa123456bbb', 4),
       substr('aaa123456bbb', 4, 6);
-- 123456bbb	123456	123456bbb	123456

-- 前后截取
SELECT LEFT('aaa123456bbb', 3),
       LEFT('aaa123456bbb', -3),
       RIGHT('aaa123456bbb', 3),
       RIGHT('aaa123456bbb', -3);
-- aaa	
-- aaa123456	
-- bbb	
-- 123456bbb

-- 去除前后缀，两种写法
SELECT TRIM(LEADING 'abc' FROM 'aaa123456bbb'),
       TRIM(TRAILING 'abc' FROM 'aaa123456bbb'),
       TRIM(BOTH 'abc' FROM 'aaa123456bbb');
-- 123456bbb	aaa123456	123456
select ltrim('aaa123456bbb', 'abc'),
       rtrim('aaa123456bbb', 'abc'),
       btrim('aaa123456bbb', 'abc');
-- 123456bbb	aaa123456	123456
```

#### 字符填充

```sql
-- lpad(x,y,z),用z在x左边补z,补齐成y长度
-- lpad(x,y,z),用z在x右边补z,补齐成y长度
select lpad('abc', 10, '01'),rpad('abc', 10, '01');
-- 0101010abc	abc0101010
```

#### 复制

```sql
SELECT repeat('abc', 4);
-- abcabcabcabc
```

#### 大小写

```sql
SELECT lower('ABCDEFG1234567A'),upper('abcdefg1234567a');
--  abcdefg1234567a  ABCDEFG1234567A
```

#### 切分

```sql
select regexp_split_to_array('abc_def_ghi_jkl_mn_opq','_')
```

#### 拆分

```sql
SELECT split_part('abc_def_ghi_jkl_mn_opq', '_', 2)
-- def

string_to_array(string, delimiter [, null string])
regexp_split_to_array ( string text, pattern text [, flags text ] ) → text[]
```

#### MD5

```sql
SELECT md5('123456');
-- e10adc3949ba59abbe56e057f20f883e
```

#### 判空

```sql
-- 多参数函数，实际上是返回第一个非空值
SELECT coalesce(null,'a',null,'b','c');
-- a

-- 可用于判空 
SELECT coalesce(colname,'0');
-- colname为null则返回'
```

### 2、时间函数

#### 时间函数

```sql
-- LOCALTIMESTAMP :	本地时间,不带时区
-- now():			本地时间,带时区
SELECT LOCALTIMESTAMP,floor(EXTRACT(epoch from LOCALTIMESTAMP)); 
SELECT now(),floor(extract(epoch from now())); 
-- 2021-10-21 16:47:30.909192		1634834850
-- 2021-10-21 16:47:30.909192+08	1634806050
-- 1634834850 = 1634806050 + 28800  = 1634806050 + 8*60*60
```

#### 时间截取

```sql
SELECT date_trunc('year', now()),
       date_trunc('month', now()),
       date_trunc('day', now()),
       date_trunc('hour', now()),
       date_trunc('min', now()),
       date_trunc('sec', now())
-- 2021-01-01 00:00:00+08	
-- 2021-10-01 00:00:00+08	
-- 2021-10-21 00:00:00+08	
-- 2021-10-21 17:00:00+08	
-- 2021-10-21 17:47:00+08	
-- 2021-10-21 17:47:41+08
```

#### 时间加减

```sql
在PostgreSQL中可以直接对时间进行加减运算：
SELECT now()::timestamp + '1 year';  --当前时间加1年
SELECT now()::timestamp + '1 month';  --当前时间加一个月
SELECT now()::timestamp + '1 day';  --当前时间加一天
SELECT now()::timestamp + '1 hour';  --当前时间加一个小时
SELECT now()::timestamp + '1 min';  --当前时间加一分钟
SELECT now()::timestamp + '1 sec';  --加一秒钟
select now()::timestamp + '1 year 1 month 1 day 1 hour 1 min 1 sec';  --加1年1月1天1时1分1秒
SELECT now()::timestamp + (col || ' day')::interval FROM table --把col字段转换成天，然后相加
```

#### 时间差

```sql
select date_part('day', '2019-11-18 12:05'::timestamp - '2019-10-18 12:05'::timestamp);
```

#### 时区

```sql
-- 查看时区
show time zone
-- 查看时间
select now();
-- 查看支持的时区列表
select * from pg_timezone_names;
-- 设置成东八区 北京时间 UTC+8
set time zone 'PRC'
```

#### EXTRACT:提取

```sql
SELECT EXTRACT(millennium FROM LOCALTIMESTAMP); -- 千年
SELECT EXTRACT(epoch FROM LOCALTIMESTAMP); -- 时间戳
SELECT EXTRACT(century FROM LOCALTIMESTAMP); -- 世纪，第几个世纪
SELECT EXTRACT(decade FROM LOCALTIMESTAMP); -- 十年，第几个十年
SELECT EXTRACT(year FROM LOCALTIMESTAMP); -- 年份
SELECT EXTRACT(quarter FROM LOCALTIMESTAMP); -- 季度
SELECT EXTRACT(month FROM LOCALTIMESTAMP); -- 月份
SELECT EXTRACT(week FROM LOCALTIMESTAMP); -- 周
SELECT EXTRACT(day FROM LOCALTIMESTAMP); -- 天
SELECT EXTRACT(hour FROM LOCALTIMESTAMP); -- 时位
SELECT EXTRACT(minute FROM LOCALTIMESTAMP); -- 分位
SELECT EXTRACT(second FROM LOCALTIMESTAMP); -- 秒位,也可以用sec

SELECT EXTRACT(dow FROM LOCALTIMESTAMP); -- day of week,sunday=0  monday=1 
SELECT EXTRACT(isodow FROM LOCALTIMESTAMP); -- ISO标准，sunday=7  monday=1 
SELECT EXTRACT(doy FROM LOCALTIMESTAMP); -- day of year
```

时间差

```sql
 date_part('day', '2019-11-18 12:05'::timestamp - '2019-10-18 12:05'::timestamp)
```

### 3、数字函数

#### 取绝对值

```sql
SELECT abs(-2388.23);
-- 2388.23
```

#### 取整数

```sql
-- ceil 向上取整，进一法
-- floor 向下取整
SELECT ceil(-12.34),floor(-12.34),ceil(12.34),floor(12.34);
-- 13	12	-12	-13
```

#### 取余数

```sql
SELECT mod(13,5);
-- 5
```

#### 取随机数

```sql
-- 生成0~1之间的随机小数
SELECT random();
-- 0.29679052112624

-- 生成100以内的随机正整数
SELECT ceil(random() * 100);
-- 68
```

#### 取精度

```sql
SELECT round(98.7654321),round(98.7654321,2),round(98.7654321,3);
-- 99	98.77	98.765
```

#### 截断

```sql
SELECT trunc(98.7654321),trunc(98.7654321,2),trunc(98.7654321,3);
-- 98	98.76	98.765
```

### 3、聚集函数

#### 常用聚集函数

```sql
-- 计数
count()
-- 求和
sum()
-- 取平均值
avg()
-- 取最大值
max()
-- 取最小值
min()
-- 连接函数，输出字符串
STRING_AGG(... , 分隔符 order by ...)   -- 排序可缺省
-- 连接函数，将多行字段合成数组
ARRAY_AGG(... order by ...) -- 排序可缺省
-- 和ARRAY_AGG函数相反，将数组拆成多行
UNNEST(...)       
```

#### 窗口函数

```sql
-- 分组并给每组计数
count(...) over(partition by ... order by ...)

-- 分组并取所选列的和
sum(col) over(partition by ... order by ...)

-- 分组并取所选列的平均值
avg(col) over(partition by ... order by ...)

-- 分组并取所选列的最大值
max(col) over(partition by ... order by ...)

-- 分组并取所选列的最小值
min(col) over(partition by ... order by ...)

-- 分组并给每组按行编号
row_number() over(partition by ... order by ...)

-- 分组并给每组排名,排名支持并列
rank() over(partition by ... order by ...) 

-- 分组并给每组排名,排名不支持并列
dense_rank() over(partition by ... order by ...)

-- 分分组并取所选列的第一位值
first_value(col) over(partition by ... order by ...) 

-- 分组并取所选列的最后一位值
last_value(col) over(partition by ... order by ...) 

-- 分组并取所选列的正序第n行值,n表示偏移位，m表示默认值(可缺省)
lag(col,n,m) over(partition by ... order by ...) 

-- 分组并取所选列的倒叙第n行值,n表示偏移位，m表示默认值(可缺省)
lead(col,n,m) over(partition by ... order by ...) 
```

### 4、类型转换

#### 类型强转

```sql
-- 类型强转分为两种方式
-- 1、使用CAST函数		CAST(XXXX AS 类型)
-- 2、使用双冒号		   :: 类型 
SELECT CAST(123 AS VARCHAR), CAST('000123' AS INT);
SELECT 123::VARCHAR, '000123' ::INT;
-- 123  123
```

#### 字符转日期

```sql
SELECT	to_date('08 OCT 2021', 'DD Mon YYYY');
-- 2021-10-08
SELECT	to_date('2021-10-08 12:34:56', 'YYYY-MM-DD HH24:mi:ss');
-- 2021-10-08
SELECT	to_timestamp('2021-10-08 12:34:56', 'YYYY-MM-DD HH24:mi:ss');
-- 2021-10-08 12:34:56+08
```

#### 日期转字符

```sql
SELECT	to_char(interval '2021year 10month 21day 15hour 2min 12sec', 'YYYY-MM-DD HH24:mi:ss');
-- 2021-10-21 15:02:12
SELECT	to_char(now(), 'YYYY-MM-DD HH24:mi:ss');
-- 2021-10-21 17:51:09
SELECT	to_char(now(), 'YYYY-MM-DD HH24:mi:ss');
-- 2021-10-21 17:51:09
```

#### 日期转时间戳unix

```sql
SELECT floor(EXTRACT(epoch FROM LOCALTIMESTAMP));
-- 1634838820
```

#### 时间戳unix转日期

```sql
SELECT TO_TIMESTAMP(1634838820);
-- 2021-10-22 01:53:40+08
```

#### 数字转字符

```sql
-- FM00...,待转化值位数少于格式位数，补0;待转化值位数多于格式位数，输出：###...
-- FM99...,待转化值位数少于格式位数，不变;待转化值位数多于格式位数，输出：###...
SELECT TO_CHAR(123456, 'FM00000000'), TO_CHAR(123456, 'FM0000'),
       TO_CHAR(123456, 'FM99999999'), TO_CHAR(123456, 'FM9999')	
-- 00123456	####	
-- 123456	####

```

#### 字符转数组

```sql
select string_to_array('aaa,bbb,ccc',',')
```

### 5、序列

#### 序列函数

```sql
-- 序列函数
currval(seqName)  					-- 返回最近一次用 nextval 获取的指定序列的数值
nextval(seqName) 					-- 递增序列并返回新值
setval(seqName, 10000) 				-- 设置当前序列值为10000，调用nextval(seqName)为10001
setval(seqName, 10000, true)		-- 设置当前序列值为10000，调用nextval(seqName)为10001
setval(seqName, 10000, false)		-- 设置当前序列值为10000，调用nextval(seqName)为10000
```

#### 创建序列

```sql
-- 创建序列关键字含义
INCREMENT BY 		-- 递增值
MINVALUE 			-- 序列最小值，NO MINVALUE表示没有最小值
MAXVALUE 			-- 序列最大值，NO MAXVALUE表示没有最大值
START WITH 			-- 从几开始
CYCLE 				-- 是否循环使用，NO CYCLE表示不循环
OWNED BY 			-- 指定到表字段，可以缺省

-- 创建序列
CREATE SEQUENCE seq_test_s INCREMENT BY 1 MINVALUE 0 NO MAXVALUE START WITH 1 NO CYCLE;
```

#### 操作序列

```sql
-- 操作序列
SELECT CURRVAL('seq_test_s');
SELECT NEXTVAL('seq_test_s');
SELECT SETVAL('seq_test_s', 1000);
SELECT SETVAL('seq_test_s', 1000, FALSE);   
SELECT SETVAL('seq_test_s', 1000, TRUE);     -- 等同于SELECT SETVAL('seq_test_s', 1000);
```

#### 查询序列

```sql
SELECT sequence_schema AS userName,
       sequence_name AS seqName,
       NEXTVAL(sequence_name) AS seqValue,
       'DROP SEQUENCE ' || sequence_name || ';' AS dropSql
  FROM information_schema.sequences
 ORDER BY sequence_schema, sequence_name ASC;

SELECT pu.usename AS userName,
       pc.relname AS seqName,
       NEXTVAL(pc.relname ::VARCHAR) AS seqValue,
       'DROP SEQUENCE ' || pc.relname || ';' AS dropSql
  FROM pg_class pc
  LEFT JOIN pg_user pu
    ON pc.relowner = pu.usesysid
 WHERE relkind = 'S'
 ORDER BY pu.usename, pc.relname ASC;
```

#### 删除序列

```sql
DROP SEQUENCE seq_test_s;
```

### 6、Pg扩展

#### 查看扩展

```sql
-- 查看已有的扩展：
SELECT * FROM pg_extension;
-- 查看可用扩展：
SELECT * FROM pg_available_extensions;
```

#### 安装扩展

```Sql
-- 安装uuid扩展
CREATE EXTENSION "uuid-ossp";
-- 使用uuid生成函数
SELECT uuid_generate_v4();

-- 安装pgcrypto扩展
CREATE EXTENSION pgcrypto;
-- 生成随机uuid
SELECT gen_random_uuid();
```

#### 卸载扩展

```sql
-- 删除扩展
DROP EXTENSION pgcrypto;
DROP EXTENSION "uuid-ossp";
```

### 7、查询进程

#### 查进程

```Sql
SELECT query,
       'SELECT pg_terminate_backend(' || pid || ');' AS kill_sql,
       usename,
       application_name,
       client_addr,
       query_start,
       state
  FROM pg_stat_activity
 WHERE datname = 'skkj_db'
   AND usename = 'skkj_db'
   AND query NOT LIKE '%PostgreSQL JDBC Driver%'
 ORDER BY query_start DESC
```

#### 杀进程

```sql
SELECT pg_terminate_backend(pid);
```

### 8、查询表字段

#### 查询所有表

```sql
-- 查询表
SELECT * FROM information_schema.tables WHERE table_schema = 'information_schema';
-- 查询字段
SELECT * FROM information_schema.columns WHERE table_name = 't_quanchao_temp';
-- 查询视图
SELECT * FROM information_schema.sequences WHERE sequence_schema = 'public';
-- 查询所有函数/存储过程
SELECT * FROM information_schema.routines WHERE routine_schema  = 'public';
```

#### 查询表字段

```sql
-- 可查询 字段名、默认值、是否为空、字段类型、字段顺序
SELECT table_name,
       column_name,
       column_default,
       is_nullable,
       data_type,
       ordinal_position,
       udt_name
  FROM information_schema.columns
 WHERE table_name = 't_organization';
```

#### pg_class&pg_attribute

```sql
-- 包括 字段名、字段类型(带长度)、是否为空、字段注释
SELECT A.attname AS NAME,
       format_type(A.atttypid, A.atttypmod) AS TYPE,
       A.attnotnull AS NOTNULL,
       col_description(A.attrelid, A.attnum) AS COMMENT
  FROM pg_class AS C, pg_attribute AS A
 WHERE C.relname = 'table_name'
   AND A.attnum > 0
   AND A.attrelid = C.oid;
```

### 9、表空间

#### 查询函数

```sql
-- 将大小格式化为单位
pg_size_pretty()

-- 查看表大小,包括索引
select pg_size_pretty(pg_total_relation_size('t_sys_calendar')); 
-- 查看表大小,不包括索引
select pg_size_pretty(pg_relation_size('t_sys_calendar'));  
-- 查看表中索引大小
select pg_size_pretty(pg_indexes_size('t_sys_calendar'));

-- 查看数据库大小，不计算索引
select pg_size_pretty(pg_database_size('skkj_db'));
-- 查看表空间大小
SELECT spcname FROM pg_tablespace;
select pg_size_pretty(pg_tablespace_size('pg_default'));
```

#### 查询表空间

```Sql
-- 查询表空间
SELECT TABLE_NAME,
       pg_size_pretty(pg_total_relation_size('"' || TABLE_NAME || '"')) AS TOTAL_SIZE,
	   pg_size_pretty(pg_relation_size('"' || TABLE_NAME || '"')) 		AS TABLE_SIZE,
       pg_size_pretty(pg_indexes_size('"' || TABLE_NAME || '"')) 		AS INDEX_SIZE
  FROM information_schema.tables
 WHERE table_schema = 'public'
   AND table_catalog = 'skkj_db'
   AND table_type = 'BASE TABLE'
 ORDER BY pg_total_relation_size('"' || TABLE_NAME || '"') DESC;
 
-- 清理表空间
VACUUM FULL ANALYZE t_quanchao;
VACUUM FULL ANALYZE t_quanchao_temp;
-- 重建索引
REINDEX TABLE t_quanchao;
REINDEX TABLE t_quanchao_temp;

-- 查看某个模式大小，包括索引。不包括索引用pg_relation_size
SELECT schemaname,
       pg_size_pretty(SUM(Pg_total_relation_size(schemaname ||'.' ||tablename))) AS TOTAL_SIZE,
       pg_size_pretty(SUM(pg_relation_size(schemaname ||'.' ||tablename))) AS TABLE_SIZE
FROM   pg_tables
WHERE  schemaname = 'public'
GROUP  BY 1; 
```

#### VACUUM清理表空间

```sql
-- 清理表空间
VACUUM FULL ANALYZE t_quanchao;
VACUUM FULL ANALYZE t_quanchao_temp;

-- 描述
VACUUM:回收已删除元组占据的存储空间。 
在一般的PostgreSQL操作里，那些已经DELETE的元组或者被UPDATE过后的元组是没有从它们所属的表中物理删除的，在完成VACUUM之前它们仍然存在。 
对一些删除修改操作比较频繁且数据量比较大的表，需要定期进行VACUUM空间回收清理
如果没有参数（VACUUM后面不加table表名），则回收当前数据库里所有表
如果有参数（VACUUM后面加table表名），则只回收目标表
简单的VACUUM（没有FULL）只是简单地回收空间并且令其可以再次使用；

-- 语法 
VACUUM [FULL|FREEZE] [VERBOSE] [table]
VACUUM [FULL|FREEZE] [VERBOSE] ANALYZE [table [(column [, …])]]

-- 关键字解释
FULL 		-- 选择"完全"清理，这样可以恢复更多的空间， 但是花的时间更多并且在表上施加了排它锁。
FREEZE 		-- 选择激进的元组"冻结"。
VERBOSE 	-- 为每个表打印一份详细的清理工作报告。
ANALYZE 	-- 更新用于优化器的统计信息，以决定执行查询的最有效方法。
table 		-- 要回收清理的表的名称（可以有模式修饰）。缺省时是当前数据库中的所有表。
column 		-- 要分析的具体的列/字段名称。缺省时是所有列/字段。

```

### 10、with子句

#### 递归查询

##### 查询sql

```sql
-- 递归查询
WITH RECURSIVE tr AS
 (SELECT t1.id,
         t1.code,
         t1.parent_code,
         (t1.parent_code || '-' || t1.code) AS codePath,
         t1.name,
         t1.full_name,
         t1.full_name ::TEXT AS areaPath,
         t1.sort
    FROM t_recursive_areas t1
   WHERE t1.code = '420000'
  UNION ALL
  SELECT t2.id,
         t2.code,
         t2.parent_code,
         (tr.codePath || '-' || t2.code) AS idPath,
         t2.name,
         t2.full_name,
         (tr.areaPath || '-' || t2.full_name) AS areaPath,
         t2.sort
    FROM t_recursive_areas t2
   INNER JOIN tr
      ON tr.code = t2.parent_code)
SELECT DISTINCT id          AS demoId,
                code        as code,
                parent_code AS parentCode,
                codePath    AS codePath,
                name        AS areaName,
                full_name   AS fullName,
                areaPath    AS areaPath,
                sort        AS sort
  FROM tr
 ORDER BY parent_code,sort;
```

##### java递归

```java
    /**
     * 为当前对象构造下级
     *
     * @param currObjList 当前对象
     * @param allObjList 所有对象
     */
    private void buildChildrenList(List<Object> currObjList, List<Object> allObjList) {
        //遍历currObjList
        for (Object currObj: currObjList) {
            //初始化currObj的childrenList
            List<Object> childrenList = new ArrayList<>();
            if(currObj.getChildrenList() != null ){
                childrenList = currObj.getChildrenList();
            }
            //从allObjList中查找下级，并添加进childrenList
            for (Object obj : allObjList) {
                if (obj.getParentId().equals(currObj.getId())) {
                    childrenList.add(obj);
                }
            }
            //递归，构造下下级...
            buildChildrenList(childrenList,allObjList);
            currObj.setChildrenList(childrenList);
        }
    }


    /**
     * 网上借鉴
     * 
     * @param code 关联code
     * @param allObjList 所有数据
     * @return list
     */
    private static List<RecursiveAreas> getChild(String code, List<Object> allObjList) {
        List<Object> childrenList = new LinkedList<>();
        for (Object obj : allObjList) {
            if (code.equals(obj.getParentCode())) {
                Object treeVO = new Object();
                treeVO.setId(obj.getId());
                treeVO.setCode(obj.getCode());
                treeVO.setParentCode(obj.getParentCode());
                treeVO.setName(obj.getName());
                treeVO.setFullName(obj.getFullName());
                treeVO.setSort(obj.getSort());
                treeVO.setChildrenList(getChild(obj.getCode(), allObjList));
                childrenList.add(treeVO);
            }
        }
        return childrenList;
    }
```

#### merge into转写

##### 临时表

```sql
WITH update_records AS(
UPDATE 主表 t1
   SET 主表字段 = 副表字段
  FROM 副表 t2
 WHERE 更新条件
 RETURNING t1.*)
INSERT INTO 主表 
  (更新字段)
  SELECT 更新字段
    FROM 副表 t2
   WHERE NOT EXISTS (SELECT 1 FROM update_records us WHERE更新条件);
```

##### 批量merge

```sql
WITH update_records AS(
UPDATE t_quanchao t1
   SET t1.name        = t2.name,
       t1.description = t2.description,
       t1.create_id   = t2.create_id,
       t1.create_date = t2.create_date,
       t1.update_id   = t2.update_id,
       t1.update_date = t2.update_date 
  FROM t_quanchao_temp t2
 WHERE t1.id = t2.id
 RETURNING t1.*)
INSERT INTO t_quanchao 
  (id, name, description, create_id, create_date, update_id, update_date)
  SELECT t2.id,
         t2.name,
         t2.description,
         t2.create_id,
         t2.create_date,
         t2.update_id,
         t2.update_date
    FROM t_quanchao_temp t2
   WHERE NOT EXISTS (SELECT 1 FROM update_records us WHERE t2.id = us.id);
```

##### 单条merge

```sql
WITH update_set AS
 (UPDATE t_merge_demo t1 
  	 SET name = #{mergeParam.name}, 
  	  	 age = #{mergeParam.age}, 
  	  	 update_user_id = #{mergeParam.updateUserId}, 
  	  	 update_time = NOW() 
   WHERE id = #{mergeParam.id} 
  RETURNING t1.*)
INSERT INTO t_merge_demo
  (id, name, age, update_user_id, update_time)
  SELECT NEXTVAL('t_merge_id_s'),
         #{mergeParam.name},
         #{mergeParam.age},
         #{mergeParam.updateUserId},
         NOW()
   WHERE NOT EXISTS (SELECT 1 FROM update_set us WHERE id = #{mergeParam.id};
```

#### 删除并备份

```sql
WITH delete_records AS
 (DELETE FROM 正式表 WHERE 删除条件 RETURNING *) 
 INSERT INTO 备份表
 (SELECT 备份字段 FROM delete_records);
```

**
