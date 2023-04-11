# zookeeper-安装

## 1 官网下载

官网下载地址：

https://kafka.apache.org/downloads      

http://archive.apache.org/dist/kafka/ 

https://dlcdn.apache.org/kafka/3.2.0/kafka_2.13-3.2.0.tgz

## 2 安装解压

```shell
tar zxvf kafka_2.13-2.8.0.tgz
```

## 3 修改配置

### 3.1 修改server.properties文件

broker.id：集群节点id编号，单机模式不用修改
listeners：默认监听9092端口
log.dirs：注意：这个目录不是存储日志的，是存储Kafka中核心数据的目录，这个目录默认是指向的tmp目录，所以建议修改一下
zookeeper.connect：kafka依赖的zookeeper

```shell
[root@localhost config]# vim server.properties
....

# broker.id属性在kafka集群中必须要是唯一
broker.id=0 
# kafka部署的机器ip和提供服务的端口号,切勿设0.0.0.0可能报错
listeners=PLAINTEXT://192.xxx.xx.xx:9092
#端口号
port=9092 
#单机可直接用localhost
host.name=localhost 
#日志存放路径可修改可不修改
log.dirs=/usr/local/install/kafka-3.2.0/log 
#zookeeper地址和端口，单机配置部署，localhost:2181 
zookeeper.connect=localhost:2181 
```

### 3.2 修改producer.properties文件

 进入kafka目录下的config目录下

```shell
[root@localhost config]# vim producer.properties
......
dataDir=/usr/local/install/kafka-3.2.0/data
dataLogDir=/usr/local/install/kafka-3.2.0/log
clientPort=2181 
maxClientCnxns=100 
tickTime=2000 
initLimit=10
```

## 4 启动运行

### 启动kafka的server

后台运行Kafka服务

```shell
nohup ./bin/kafka-server-start.sh config/server.properties &
```

### 查看是否运行成功

```shell
jps -l
```

### 创建一个topic

```shell
./bin/kafka-topics.sh --zookeeper localhost:2181 --create --topic SUBJECT_SEARCH_TOPIC --replication-factor 1 --partitions 1
```

