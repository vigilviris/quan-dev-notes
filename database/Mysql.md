#### 聚合函数

```Sql
AVG(col)								-- 平均值
COUNT(col)								-- 计数
SUM(col)								-- 求和
MIN(col)								-- 最小值
MAX(col)								-- 最大值
GROUP_CONCAT(col) 						-- 取字段值连接而成字符串
```

#### 数学函数

```sql
-- 数学函数
ABS(x) 									-- 取绝对值
SQRT(x)  								-- 开平方
MOD(x,y)    							-- 取余/求模
CEIL(x)/CEILING(x)  					-- 进一取整
FLOOR(x) 								-- 退一取整/去除小数位
ROUND(x)								-- 四舍五入
ROUND(x,y)  							-- 四舍五入(带精度)
SIGN(x)     							-- 判断正负
POW(x,y)/POWER(x,y)  					-- 求乘方
FORMAT(x,n)								-- 数字格式化
CONV(N,from_,to_)						-- 进制转换
```

#### 字符串函数

```sql
-- 字符串函数
CHAR_LENGTH(str)						-- 字符串长度
LOCATE(s1,STR,x)						-- 返回字符串下标,str:原字符串; x:起始下标; s1:目标字符串;
POSITION(s1 IN str)						-- 同上
INSTR(str,s1)							-- 同上
CONCAT(s1,s2...)						-- 字符串拼接，任意一个为NULL则返回值为NULL
CONCAT_WS(x,s1,s2,...)					-- 字符串拼接，每个字符串之间有一个x
REPLACE(str,s1,s2)						-- 字符串替换, str:原字符串; s1:被替换字符串; s2:新字符串;
INSERT(str,x,len,s1)					-- 字符串替换，str:原字符串; x:起始下标; len:偏移长度; s1:新字符串;
SUBSTRING(str,x,len)					-- 截取字符串, str:原字符串; x:起始下标; len:偏移长度;
MID(str,n,len)							-- 同SUBSTRING(str,n,len)
LEFT(str,n)								-- 左边截取字符串
RIGHT(str,n)							-- 右边截取字符串
LPAD(str,len,s1)						-- 字符串格式化，str:原字符串; len:格式化长度; s1:左端填充字符串; 
RPAD(str,len,s1)						-- 字符串格式化，str:原字符串; len:格式化长度; s1:右端填充字符串;
LOWER(str)和LCASE(str)					-- 转小写
UPPER(str)和UCASE(str)					-- 转大写
TRIM(s)									-- 去除两边空格
LTRIM(s)								-- 去除左边空格
RTRIM(s)								-- 去除右边空格
TRIM(s1 FROM str)						-- 去除两端字符串
REPEAT(str,n)							-- 返回一个由重复字符串s组成的字符串，字符串s的数目等于n
SPACE(n)								-- 返回一个由n个空格组成的字符串
STRCMP(s1,s2)							-- 字符串比较。s1=s2返回0; s1>s2返回1; s1<s2返回-1;
REVERSE(str)							-- 反转字符串
ELT(N,str1,str2,str3,...) 				-- 返回第N个字符串
```

#### 日期时间函数

```Sql
-- %Y%m%d%H%i%s
-- %Y-%m-%d %H:%i:%s
-- %Y-%m-%d
-- %Y/%m/%d
-- %H:%i:%s
-- 日期时间函数
STR_TO_DATE()							-- 字符串转日期
DATE_FORMAT()							-- 日期格式化(日期转字符串)
DATEDIFF() 								-- 取日期间隔
TIMESTAMPDIFF()							-- 取时间间隔，DAY,HOUR,MINUTE,SECOND
DATE_ADD()/DATE_SUB()					-- 日期加减
CURDATE()								-- 取当前日期，YYYY-MM-DD
SYSDATE()								-- 取当前时间，YYYY-MM-DD HH:MM:SS
UNIX_TIMESTAMP()						-- 取时间戳(格林尼治标准时间)，无参默认取当前时间戳
FROM_UNIXTIME()							-- 时间戳格式化
YEAR()									-- 取当前年
MONTH()									-- 取当前月
DAY()									-- 取当前天
HOUR()									-- 取当前时
MINUTE()								-- 取当前分
SECOND()								-- 取当前秒
WEEK()									-- 取当前第几周
QUARTER()								-- 取当前季度
MONTHNAME()								-- 当前月份名
DAYNAME()								-- 当前工作日												
DAYOFWEEK()								-- 当前工作日索引,1表示周日、2表示周一....
WEEKDAY()								-- 当前工作日索引,0表示周一，1表示周二...
DAYOFYEAR()								-- 当年第几天
DAYOFMONTH()							-- 当月第几天
TIME_TO_SEC()							-- 时间转秒数
SEC_TO_TIME()							-- 秒数转时间
```

#### 内置对象查询

```sql
--表
SELECT * from information_schema.TABLES 
--视图
SELECT * from information_schema.VIEWS
--存储过程
select name from mysql.proc where db = 'zentao' and type = 'PROCEDURE' 
--函数
select name from mysql.proc where db = 'zentao' and type = 'FUNCTION'
```

