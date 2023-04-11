| Tag                                        | Comment                                                     |                                          |
| :----------------------------------------- | ----------------------------------------------------------- | ---------------------------------------- |
| ch.CHModel `ch:"table:table_name"`         | Override default table name.                                | 覆盖默认表名。                           |
| ch.CHModel `ch:"alias:table_alias"`        | Override default table alias.                               | 覆盖默认表别名。                         |
| ch.CHModel `ch:"insert:table_name_buffer"` | Override table name for `INSERT` queries.                   | 覆盖查询的表名INSERT。                   |
| ch:"-"                                     | Ignore the field.                                           | 忽略字段。                               |
| ch:"column_name"                           | Override default column name.                               | 覆盖默认列名。                           |
| ch:"alt:alt_name"                          | Alternative column name. Useful during migrations.          | 替代列名。在迁移期间很有用。             |
| ch:",pk"                                   | Mark column as a ClickHouse primary key (order by).         | 将列标记为 ClickHouse 主键（排序依据）。 |
| ch:"type:uuid"                             | Override default SQL type.                                  | 覆盖默认 SQL 类型。                      |
| ch:"default:gen_random_uuid()"             | Tell `CreateTable` to set `DEFAULT` expression.             | 告诉CreateTable设置DEFAULT表达式。       |
| ch:",notnull"                              | Tell `CreateTable` to add `NOT NULL` constraint.            | 告诉CreateTable添加NOT NULL约束。        |
| ch:",scanonly"                             | Only use this field to scan query results, not for inserts. | 仅使用此字段扫描查询结果，而不用于插入。 |
| ch:",array"                                | Use PostgreSQL array.                                       | 使用 PostgreSQL 数组。                   |
| ch:",json_use_number"                      | Use `json.Decoder.UseNumber` to decode JSON                 | 用于json.Decoder.UseNumber解码 JSON。    |
