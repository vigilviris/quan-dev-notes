

## 1 下载

官网下载地址：https://downloads.mysql.com/archives/community/

#### 1.1 安装包解压

```shell
#先将.tar.xz解压至.tar
xz -d mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz
#再继续解压.tar
tar -xvf mysql-8.0.25-linux-glibc2.12-x86_64.tar
 #重命名
mv mysql-8.0.25-linux-glibc2.12-x86_64 mysql-8.0.25
```

##  2 安装配置

#### 2.1 创建用户

```shell
#创建用户组
groupadd mysql
#创建用户
useradd -g mysql mysql
```

#### 2.2 创建存放数据的目录

```shell
#创建并修改目录拥有者为mysql
cd /usr/local/install/mysql-8.0.25
mkdir data
chown -R mysql:mysql data
```

#### 2.3 初始化数据库

```shell
#初始化
cd /usr/local/install/mysql-8.0.25
./bin/mysqld --initialize --user=mysql --basedir=/usr/local/install/mysql-8.0.25 --datadir=/usr/local/install/mysql-8.0.25/data

..............
...初始化过程...
..............
# mysql初始密码，此密码很重要，要记住，后面要用
....A temporary password is generated for root@localhost: )d7b<+SkV!BZ

```

#### 2.4 编辑配置文件

```shell
#编辑配置文件
vim /etc/my.cnf

[mysqld]
user=mysql
basedir=/usr/local/install/mysql-8.0.25
datadir=/usr/local/install/mysql-8.0.25/data
socket=/tmp/mysql.sock
[mysql]
socket=/tmp/mysql.sock

#创建
mkdir /var/log/mariadb
touch /var/log/mariadb/mariadb.log
#授权
chown -R mysql:mysql /var/log/mariadb/
```

#### 2.5 配置环境变量

```shell
#编辑 
vim /etc/profile
 
export MYSQL_HOME=/usr/local/install/mysql-8.0.25
export PATH=$PATH:$MYSQL_HOME/bin
 
# 生效
source /etc/profile
```

## 3 运行启动

#### 3.1 首次启动MySql服务

```shell
#进入support-files目录运行启动脚本
cd /usr/local/install/mysql-8.0.25/support-files
./mysql.server start

Starting MySQL.Logging to '/usr/local/install/mysql-8.0.25/data/localhost.localdomain.err'.
.. SUCCESS! 
```

#### 3.2 使用systemctl方式管理MySql

```shell
#复制mysql.server到/etc/init.d/mysqld
cd /usr/local/install/mysql-8.0.25/support-files
cp mysql.server  /etc/init.d/mysqld
#添加可执行权限。
chmod +x /etc/init.d/mysqld

# CentOS注册启动服务
chkconfig --add mysqld
#查看是否添加成功
chkconfig  --list

# Ubantu注册启动服务
sudo systemctl enable mysqld

systemctl list-unit-files
注：该输出结果只显示 SysV 服务，并不包含
原生 systemd 服务。SysV 配置数据
可能被原生 systemd 配置覆盖。 
      要列出 systemd 服务，请执行 'systemctl list-unit-files'。
      查看在具体 target 启用的服务请执行
      'systemctl list-dependencies [target]'。

mysqld         	0:关	1:关	2:开	3:开	4:开	5:开	6:关
netconsole     	0:关	1:关	2:关	3:关	4:关	5:关	6:关
network        	0:关	1:关	2:开	3:开	4:开	5:开	6:关


```

#### 3.3 启动

```shell
# 启动MySql服务
systemctl start mysqld
# 查看运行状态
systemctl status mysql.service
# 停止
systemctl stop mysqld
```

## 4 登录MySql

#### 4.1 重置密码

```shell
mysql -uroot -p init123
#输入初始密码
Enter password:
#重置密码
mysql> alter user 'root'@'localhost' identified by 'Init1234';
Query OK, 0 rows affected (0.02 sec)
```

#### 4.2 用户

```shell
#新增用户：
mysql> create user 'nacos'@'%' identified by 'Init1234';
mysql> grant all privileges on *.* to nacos@'%';
```

#### 4.3 设置允许远程登录

```shell
#设置允许远程登录
#1.update改表法
mysql> user mysql;
mysql> update user set user.Host='%' where user.User='nacos';

#2.grant授权法(8.0以前)
mysql> grant all privileges on *.* to root@'%';
#刷新权限
mysql> flush privileges; 
```

#### 4.4 关闭防火墙

```shell
# firewall防火墙
[root@localhost ~]# systemctl status firewalld
# 查看转态
[root@localhost ~]# firewall-cmd --state
# 开启
[root@localhost ~]# service firewalld start
# 重启
[root@localhost ~]# service firewalld restart
# 关闭
[root@localhost ~]# service firewalld stop
#注意分清楚linux的版本 命令会有所不同
#查看防火墙规则
[root@localhost ~]# firewall-cmd --list-all 
```

## 5 安装依赖

```shell
error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory
error while loading shared libraries: libtinfo.so.5: cannot open shared object file: No such file or directory

#安装 libaio.so.1
apt-get install libaio1
apt-get install libtinfo5
```
