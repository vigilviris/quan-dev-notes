| **参数**                | **默认值** | **是否必传** | **描述信息**                                                 |
| ----------------------- | ---------- | ------------ | ------------------------------------------------------------ |
| -f 或 --file            |            | Y            | 配置脚本的路径                                               |
| -g 或 --graph           |            | Y            | 图数据库空间                                                 |
| -s 或 --schema          |            | Y            | schema文件路径                                               |
| -h 或 --host            | localhost  |              | HugeGraphServer 的地址                                       |
| -p 或 --port            | 8080       |              | HugeGraphServer 的端口号                                     |
| --username              | null       |              | 当 HugeGraphServer 开启了权限认证时，当前图的 username       |
| --token                 | null       |              | 当 HugeGraphServer 开启了权限认证时，当前图的 token          |
| --protocol              | http       |              | 向服务端发请求的协议，可选 http 或 https                     |
| --trust-store-file      |            |              | 请求协议为 https 时，客户端的证书文件路径                    |
| --trust-store-password  |            |              | 请求协议为 https 时，客户端证书密码                          |
| --clear-all-data        | false      |              | 导入数据前是否清除服务端的原有数据                           |
| --clear-timeout         | 240        |              | 导入数据前清除服务端的原有数据的超时时间                     |
| --incremental-mode      | false      |              | 是否使用断点续导模式，仅输入源为 FILE 和 HDFS 支持该模式，启用该模式能从上一次导入停止的地方开始导 |
| --failure-mode          | false      |              | 失败模式为 true 时，会导入之前失败了的数据，一般来说失败数据文件需要在人工更正编辑好后，再次进行导入 |
| --batch-insert-threads  | CPUs       |              | 批量插入线程池大小 (CPUs是当前OS可用**逻辑核**个数)          |
| --single-insert-threads | 8          |              | 单条插入线程池的大小                                         |
| --max-conn              | 4 * CPUs   |              | HugeClient 与 HugeGraphServer 的最大 HTTP 连接数，**调整线程**的时候建议同时调整此项 |
| --max-conn-per-route    | 2 * CPUs   |              | HugeClient 与 HugeGraphServer 每个路由的最大 HTTP 连接数，**调整线程**的时候建议同时调整此项 |
| --batch-size            | 500        |              | 导入数据时每个批次包含的数据条数                             |
| --max-parse-errors      | 1          |              | 最多允许多少行数据解析错误，达到该值则程序退出               |
| --max-insert-errors     | 500        |              | 最多允许多少行数据插入错误，达到该值则程序退出               |
| --timeout               | 60         |              | 插入结果返回的超时时间（秒）                                 |
| --shutdown-timeout      | 10         |              | 多线程停止的等待时间（秒）                                   |
| --retry-times           | 0          |              | 发生特定异常时的重试次数                                     |
| --retry-interval        | 10         |              | 重试之前的间隔时间（秒）                                     |
| --check-vertex          | false      |              | 插入边时是否检查边所连接的顶点是否存在                       |
| --print-progress        | true       |              | 是否在控制台实时打印导入条数                                 |
| --dry-run               | false      |              | 打开该模式，只解析不导入，通常用于测试                       |
| --help                  | false      |              | 打印帮助信息                                                 |

执行命令

```shell
bin/hugegraph-loader -g {GRAPH_NAME} -f ${INPUT_DESC_FILE} -s ${SCHEMA_FILE} -h {HOST} -p {PORT}

bin/hugegraph-loader.sh -g hugegraph -f example/file/struct.json -s example/file/schema.groovy -h local -p 8881

```

转化2.0

```shell
bin/mapping-convert.sh struct.json
```
