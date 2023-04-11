### 日期函数

```sql
-- 字符转日期
toDate('2022-09-09')
toDateTime('2022-09-09 10:00:00')
toDateTime('2022-09-09 10:10:10', 'Asia/Shanghai')

-- 时间格式化
formatDateTime(Time, Format[, Timezone])

-- 时间差
dateDiff('unit', start, end, [timezone])
dateAdd('unit', int, date/dateTime)
dateSub('unit', int, date)

-- 时间截取
dateTrunc('unit', int, date)

-- 日期加减 add>>subtract
addYears(date,-1)
addQuarters(date,-1)
addMonths(date,-1)
addWeeks(date,-1)
addDays(date,-1)
-- 时间加减 add>>subtract
addYears(dateTime,-1)
addQuarters(dateTime,-1)
addMonths(dateTime,-1)
addWeeks(dateTime,-1)
addDays(dateTime,-1)
addHours(dateTime,-1)
addMinutes(dateTime,-1)
addSeconds(dateTime,-1)
```



| 描述               | 函数                    | 效果            |
| ------------------ | ----------------------- | --------------- |
| 取当前时间         | now()                   | 2022-7-13 14:28 |
| 获取当前时间戳     | toUnixTimestamp(now())  | 1657695998      |
| 取日期中的年份     | toYear(now())           | 2022            |
| 取日期中的月份     | toMonth(now())          | 7               |
| 今天的日期         | today()                 | 2022-7-13       |
| 取日期中的日期     | toDate(now())           | 2022-7-13       |
| 昨天的日期         | yesterday()             | 2022-7-12       |
| 获取当前日期季度   | toQuarter(now())        | 3               |
| 当前月份的天数     | toDayOfMonth(now())     | 13              |
| 日期化为天数       | toDayOfYear(now())      | 194             |
| 获取星期几         | toDayOfWeek(now())      | 3               |
| 当前周的第一天     | toMonday(now())         | 2022-7-11       |
| 取日期时间中的小时 | toHour(now())           | 14              |
| 取日期时间中的分钟 | toMinute(now())         | 42              |
| 取日期时间中的秒   | toSecond(now())         | 40              |
| 当前季度的第一天   | toStartOfQuarter(now()) | 2022-7-1        |
| 当前起始分钟时间   | toStartOfMinute(now())  | 2022-7-13 14:57 |
| 当前起始小时时间   | toStartOfHour(now())    | 2022-7-13 14:00 |
| 当天起始时间       | toStartOfDay(now())     | 2022-7-13 0:00  |
| 当前年份的第一天   | toStartOfYear(now())    | 2022-1-1        |
| 当前月份的第一天   | toStartOfMonth(now())   | 2022-7-1        |

```sql
WITH
    toDate(now()) as date,
    toDateTime(now()) as date_time
SELECT
    subtractYears(date, 1) AS subtract_years_with_date,
    subtractYears(date_time, 1) AS subtract_years_with_date_time,
    subtractQuarters(date, 1) AS subtract_Quarters_with_date,
    subtractQuarters(date_time, 1) AS subtract_Quarters_with_date_time,
    subtractMonths(date, 1) AS subtract_Months_with_date,
    subtractMonths(date_time, 1) AS subtract_Months_with_date_time,
    subtractWeeks(date, 1) AS subtract_Weeks_with_date,
    subtractWeeks(date_time, 1) AS subtract_Weeks_with_date_time,
    subtractDays(date, 1) AS subtract_Days_with_date,
    subtractDays(date_time, 1) AS subtract_Days_with_date_time,
    subtractHours(date_time, 1) AS subtract_Hours_with_date_time,
    subtractMinutes(date_time, 1) AS subtract_Minutes_with_date_time,
    subtractSeconds(date_time, 1) AS subtract_Seconds_with_date_time;
 
SELECT toDate('2019-07-31', 'Asia/GuangZhou') as date_guangzhou;
SELECT toDate('2019-07-31'), toDate('2019-07-31', 'Asia/Beijing') as date_beijing;
 
-- 亚洲只能加载上海的timezone？？？
SELECT toDateTime('2019-07-31 10:10:10', 'Asia/Shanghai') as date_shanghai;
```

### ALTER

```sql
-- 添加列
ALTER TABLE t_name ADD COLUMN col_name String AFTER after_col_name;
-- 删除列
ALTER TABLE t_name DROP COLUMN col_name
-- 添加备注
ALTER TABLE t_name COMMENT COLUMN col_name '备注';
-- 更改列类型
ALTER TABLE t_name MODIFY COLUMN col_name string;
```



| 修饰符 | 描述                                                         | 示例       |
| ------ | ------------------------------------------------------------ | ---------- |
| %F     | 短YYYY-MM-DD日期，相当于%Y-%m-%d                             | 2018-01-02 |
| %D     | 短MM/DD/YY日期，相当于%m/%d/%y                               | 01/02/2018 |
| %T     | ISO8601时间格式(HH:MM:SS)，相当于%H:%M:%S                    | 22:33:44   |
| %R     | 24小时HH:MM时间，相当于%H:%M                                 | 22:33      |
| %Y     | 年                                                           | 2018       |
| %m     | 月份为十进制数（01-12)                                       | 01         |
| %d     | 月中的一天，零填充（01-31)                                   | 02         |
| %H     | 24小时格式（00-23)                                           | 22         |
| %M     | 分钟(00-59)                                                  | 33         |
| %S     | 秒 (00-59)                                                   | 44         |
| %I     | 12小时格式（01-12)                                           | 10         |
| %C     | 年除以100并截断为整数(00-99)                                 | 20         |
| %y     | 年份，最后两位数字（00-99)                                   | 18         |
| %e     | 月中的一天，空格填充（ 1-31)                                 | 2          |
| %G     | ISO周号的四位数年份格式， 从基于周的年份[由ISO 8601定义](https://en.wikipedia.org/wiki/ISO_8601#Week_dates) 标准计算得出，通常仅对％V有用 | 2018       |
| %g     | 两位数的年份格式，与ISO 8601一致，四位数表示法的缩写         | 18         |
| %j     | 一年中的一天 (001-366)                                       | 002        |
| %n     | 换行符(")                                                    |            |
| %p     | AM或PM指定                                                   | PM         |
| %Q     | 季度（1-4)                                                   | 1          |
| %t     | 水平制表符(’)                                                |            |
| %u     | ISO8601工作日为数字，星期一为1(1-7)                          | 2          |
| %V     | ISO8601周编号(01-53)                                         | 01         |
| %w     | 工作日为十进制数，周日为0(0-6)                               | 2          |
| %%     | %符号                                                        | %          |

### 查询表名

```sql
select t.database as schema_name,
       t.table as table_name,
       arrayStringConcat(groupArray(t.name), ',') as column_names
  from system.columns t
 where t.database = ?
   and t.table like ?
 group by t.database, t.table
 
 select name               as column_name,
       type               as column_type,
       default_expression as column_default,
       comment            as column_remark,
       position           as sort
  FROM system.columns
 where database = ?
   and table = ?
 order by position
```

### 聚合函数

```sql
select aaa, arrayStringConcat(groupArray(bbb),',') from ttt group by aaa
```

