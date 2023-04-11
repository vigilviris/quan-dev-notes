## 一、HugeGraph-Server

### 1 下载安装

下载地址：https://hugegraph.github.io/hugegraph-doc/download.html

| **工具**         | **描述**                | **版本**                                                     |
| ---------------- | ----------------------- | ------------------------------------------------------------ |
| HugeGraph-Server | HugeGraph-Server主程序  | [0.12.0](https://github.com/hugegraph/hugegraph/releases/download/v0.12.0/hugegraph-0.12.0.tar.gz) |
| HugeGraph-Hubble | 基于Web的可视化图形界面 | [1.6.0](https://github.com/hugegraph/hugegraph-hubble/releases/download/v1.6.0/hugegraph-hubble-1.6.0.tar.gz) |
| HugeGraph-Loader | 数据导入工具            | [0.12.0](https://github.com/hugegraph/hugegraph-loader/releases/download/v0.12.0/hugegraph-loader-0.12.0.tar.gz) |
| HugeGraph-Tools  | 命令行工具集            | [1.6.0](https://github.com/hugegraph/hugegraph-tools/releases/download/v1.6.0/hugegraph-tools-1.6.0.tar.gz) |

#### 2.1 安装jdk

HugeGraphServer基于jdk-8开发，代码用到了较多jdk-1.8中的类和方法，请自行安装配置

```shell
[root@VM-4-3-centos /]# java -version
java version "1.8.0_333"
Java(TM) SE Runtime Environment (build 1.8.0_333-b02)
Java HotSpot(TM) 64-Bit Server VM (build 25.333-b02, mixed mode)
```

#### 2.2 安装 GCC

- 如果使用的是RocksDB补充，执行gcc --version查看gcc版本
- 需要GLIBCXX_3.4.10及以上版本

```shell
yum install gcc-c++
gcc --version
```

### 2 解压安装

```shell
cd /usr/local/install/hugegraph
tar -zxvf hugegraph-0.12.0.tar.gz
```

### 3 配置GremlinServer

#### 3.1 修改gremlin-server.yaml文件

**host**： 部署 GremlinServer 机器的机器名或 IP

**port**： 部署 GremlinServer 机器的端口，端口自己设定，可以使用默认的8182

```shell
vim conf/gremlin-server.yaml 
# host and port of gremlin server, need to be consistent with host and port in rest-server.properties
host: 121.4.248.203
port: 8182
```

![image-20221103121634692](C:\Users\quanchao\AppData\Roaming\Typora\typora-user-images\image-20221103121634692.png)

#### 3.2 修改rest-server.properties文件

```shell
vim conf/rest-server.properties

# bind url
restserver.url=http://121.4.248.203:8881
# gremlin server url, need to be consistent with host and port in gremlin-server.yaml
gremlinserver.url=http://121.4.248.203:8182

graphs=./conf/graphs
```

![image-20221103122048937](C:\Users\quanchao\AppData\Roaming\Typora\typora-user-images\image-20221103122048937.png)

### 4 启动服务

- 启动分为"首次启动"和"非首次启动"，这么区分是因为在第一次启动前需要初始化后端数据库，然后启动服务。 而在人为停掉服务后，或者其他原因需要再次启动服务时，因为后端数据库是持久化存在的，直接启动服务即可。
- HugeGraphServer 启动时会连接补充备份并试用补充更新版本号，如果未初始化完整版本或者已完成初始化但版本不匹配时(旧版本数据)，HugeGraphServer 会启动失败，并提供错误信息。

- 如果需要指定端口号访问HugeGraphServer，请修改./conf/rest-server.properties的restserver.url配置项，修改成机器名或IP地址。这里203服务器使用的是8881端口：http://121.4.248.203:8881，可以访问该地址

#### 4.1 Memory启动方式(默认)

Memory后端的数据是保存在内存中无法持久化的，不需要初始化后端，这也是唯一一个不需要初始化的后端。

##### 4.1.1 修改 hugegraph.properties

```shell
backend=memory
serializer=text
```

##### 4.1.2 启动服务

```shell
bin/start-hugegraph.sh 

Starting HugeGraphServer...
Connecting to HugeGraphServer (http://192.168.14.157:8881/graphs)........OK
Started

# 提示的 url 与 rest-server.properties 中配置的 restserver.url 一致
```

#### 4.2 RocksDB启动方式(推荐)

RocksDB是一个嵌入式的数据库，不需要手动安装部署, 要求 GCC 版本 >= 4.3.0（GLIBCXX_3.4.10），如不满足，需要提前升级 GCC

##### 4.2.1 创建rocksdb数据文件地址

```shell
cd /usr/local/install/hugegraph/hugegraph-0.12.0
mkdir my_database
mkdir my_database/data my_database/wal
```

##### 4.2.2 修改hugegraph.properties

```shell
vim conf/graphs/hugegraph.properties

....
backend=rocksdb
serializer=binary
# 此路径需要自行指定
rocksdb.data_path=/usr/local/install/hugegraph/hugegraph-0.12.0/my_database/data
rocksdb.wal_path=/usr/local/install/hugegraph/hugegraph-0.12.0/my_database/wal
```

![img](https://cdn.nlark.com/yuque/0/2022/png/21913226/1644285681870-0fc43394-1b67-4de8-9c74-599f899aee26.png)

##### 4.2.3 首次启动初始化

仅首次启动需要，后续不需要

```shell
bin/init-store.sh


# 当看到 Initialization finished.即初始化
```

##### 4.2.4 启动服务

```shell
bin/start-hugegraph.sh

.....
Starting HugeGraphServer...
Connecting to HugeGraphServer (http://192.168.14.157:8881/graphs)........OK
Started

# 提示的 url 与 rest-server.properties 中配置的 restserver.url 一致
```

#### 4.3 其他启动方式(Cassandra、ScyllaDB、HBase)

需要自行安装，参考:https://hugegraph.github.io/hugegraph-doc/quickstart/hugegraph-server.html

### 5 停止服务

```shell
bin/stop-hugegraph.sh 
no crontab for deploy
The HugeGraphServer monitor has been closed
Killing HugeGraphServer(pid 10025)...OK
```

## 二 、HugeGraph-Hubble

### 1 解压安装

```shell
[root@VM-4-3-centos /]# cd /usr/local/install/hugegraph
[root@VM-4-3-centos hugegraph]# tar -zxvf hugegraph-hubble-1.6.0.tar.gz
```

### 2 修改配置

如果需要从另外的机器访问，需要修改文件conf/hugegraph-hubble.properties，将server.host改为0.0.0.0，post端口号可自定义

```shell
[root@VM-4-3-centos /]# cd /usr/local/install/hugegraph-hubble-1.6.0/
[root@VM-4-3-centos hugegraph-hubble-1.6.0]# vim conf/hugegraph-hubble.properties 

server.host=0.0.0.0
server.port=8882
```

### 3 启动Hubble

```shell
[root@VM-4-3-centos hugegraph-hubble-1.6.0]# bin/start-hubble.sh 
starting HugeGraphHubble.....OK
logging to /usr/local/install/hugegraph-hubble-1.6.0/logs/hugegraph-hubble.log
```

### 4 访问Hubble

使用浏览器访问http:xx.xx.xx.xx:8882，这时候需要创建图

![img](https://cdn.nlark.com/yuque/0/2022/png/21913226/1644286766014-c58dc442-aa6b-4b89-bdd3-8abd5422efb3.png)

| 图ID   | 随意取个值                                                   |
| ------ | ------------------------------------------------------------ |
| 图名称 | hugegraph(首次启动HugeGraph-Server 前，使用 init-store.sh 初始化的图) |
| 主机名 | HugeGraph-Server所在的主机的域名或IP，参考HugeGraph-Server-home}/conf/rest-server.properties中的rest.server-url |
| 端口号 | HugeGraph-Server所配置的端口号，参考HugeGraph-Server-home}/conf/rest-server.properties中的rest.server-url |
| 用户名 | 空                                                           |
| 密码   | 空                                                           |
