## 1 官网下载

官网下载地址：https://zookeeper.apache.org/releases.html

![image-20220719170818902](C:\Users\quanchao\AppData\Roaming\Typora\typora-user-images\image-20220719170818902.png)

## 2 安装解压

```shell
tar -zxvf zookeeper-3.7.0.tar.gz
mv apache-zookeeper-3.7.0-bin/ zookeeper-3.7.0
```

## 3 修改配置 

#### 拷贝文件zoo_sample.cfg生成zoo.cfg

```shell
[root@localhost conf]# cd /usr/local/install/zookeeper-3.7.0/conf/
[root@localhost conf]# cp zoo_sample.cfg zoo.cfg
```

#### 编辑zoo.cfg

- **tickTime**：ZooKeeper使用的基本时间单位（毫秒）。它用于做心跳，并且最小会话超时将是tickTime的两倍。
- **dataDir**：存储内存数据库快照的位置
- **dataLogDir**:存储日志的位置
- **clientPort**：用于侦听客户端连接的端口

```shell
[root@localhost conf]# vim zoo.cfg 

tickTime=2000
initLimit=10
syncLimit=5
dataDir=/tmp/zookeeper
dataLogDir=/tmp/zookeeper/log
clientPort=2181
```

## 4 运行启动

```
//运行 ZooKeeper Server（后台运行）
[root@localhost zookeeper3.7.0]# bin/zkServer.sh start
//非后台运行ZooKeeper Server进程
[root@localhost zookeeper3.7.0]# bin/zkServer.sh start-foreground
//停止 ZooKeeper Server
[root@localhost zookeeper3.7.0]# bin/zkServer.sh stop
//重启ZooKeeper Server
[root@localhost zookeeper3.7.0]# bin/zkServer.sh restart
//查看 ZooKeeper Server 状态
[root@localhost zookeeper3.7.0]# bin/zkServer.sh status
//运行 ZooKeeper 客户端
[root@localhost zookeeper3.7.0]# bin/zkServer.sh start
[root@localhost zookeeper3.7.0]# bin/zkCli.sh -server 127.0.0.1:2181
```

