## 1 下载

https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html

## 2 安装解压

```shell
[root@VM-4-3-centos /]# cd /usr/local/install
[root@VM-4-3-centos  install]# tar -zxvf jdk-8u333-linux-x64.tar.gz
```

## 3 配置环境变量

```shell
[root@VM-4-3-centos jdk1.8.0_333]# vim /etc/profile

export JAVA_HOME=....../jdk1.8.0_333
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin

[root@VM-4-3-centos jdk1.8.0_333]# source /etc/profile
```

## 4 版本验证

```shell
[root@VM-4-3-centos jdk1.8.0_333]# java -version
java version "1.8.0_333"
Java(TM) SE Runtime Environment (build 1.8.0_333-b02)
Java HotSpot(TM) 64-Bit Server VM (build 25.333-b02, mixed mode)
```

