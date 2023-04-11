## 启动命令

```shell
# 项目后台启动
nohup xxxxxx > out.log 2>&1 &

# nacos
bin/startup.sh -m standalone
bin/startup.sh -m cluster

#pgsql
bin/pg_ctl -D ....../pgsql/data -l ...../pgsql/log/logfile restart

# MySql
systemctl start mysqld
systemctl status mysql.service
systemctl stop mysqld

# zookeeper
bin/zkServer.sh status
bin/zkServer.sh start
bin/zkServer.sh restart

# kafka
nohup ./bin/kafka-server-start.sh config/server.properties &

# redis
bin/redis-server etc/redis.conf
```

## shell脚本

```shell
$# 是传给脚本的参数个数
$0 是脚本本身的名字
$1 是传递给该shell脚本的第一个参数,$2...往后依次推类
$@ 是传给脚本的所有参数的列表
$*	以一个单字符串显示所有向脚本传递的参数
$$	脚本运行的当前进程ID号
$!	后台运行的最后一个进程的ID号
$@	与$*相同，但是使用时加引号，并在引号中返回每个参数。
$-	显示Shell使用的当前选项，与set命令功能相同。
$?	显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。
```

### 项目启动脚本

#### go项目启动

```sh
#!/bin/bash

now_time=$(date '+%Y%m%d%H%M%S')
default_name='xxxxxx'
app_name=''

# 获取第一个参数作为应用名
if [[ $1 && $1 != 0 ]]
then
    app_name=$1
else
    app_name=$default_name
fi

# 查询应用进程ID
p_id=`ps -ef | grep ${app_name} |grep -v grep | grep -v bash | awk '{print $2}'`
if [ ${p_id}x == ""x ]
then
	echo "应用${app_name}不在运行中"
	read -p "请选择操作编号: 1-运行/0-退出 : " my_choose
	if [ ${my_choose}x == "1"x ]
	then
		rm -rf nohup.out
		mv logs/${app_name}.log logs/${app_name}_${now_time}.log
		nohup ./${app_name} &
		sleep 2
		new_pid=`ps -ef | grep ${app_name} |grep -v grep | grep -v bash | awk '{print $2}'`
		echo "${app_name} 已运行，PID为 : ${new_pid}"
	fi
else
	echo "应用${app_name}正在运行中，PID为 : “${p_id}”"
	read -p "请选择操作编号: 1-停止/2-重启/0-退出 : " my_choose
	if [ ${my_choose}x == "1"x ]
	then
		kill -9 ${p_id}
		echo "${app_name} 已停止"
	elif [ ${my_choose}x == "2"x ]
	then
		kill -9 ${p_id}
		echo "${app_name} 已停止，正在重启中..."
		rm -rf nohup.out
		mv logs/${app_name}.log logs/${app_name}_${now_time}.log
		nohup ./${app_name} &
		sleep 2
		new_pid=`ps -ef | grep ${app_name} |grep -v grep | grep -v bash | awk '{print $2}'`
		echo "${app_name} 已重启，PID为 : ${new_pid}"
	fi
fi
exit

```

#### java项目启动

```shell
#!/bin/bash

java_jar='java -jar -Xms512m -Xmx512m'
default_name='xxxxx'
app_name=''

# 获取第一个参数作为应用名
if [[ $1 && $1 != 0 ]]
then
    app_name=$1
else
    app_name=$default_name
fi

# 查询应用进程ID
p_id=`ps -ef | grep ${app_name} |grep -v grep | grep -v bash | awk '{print $2}'`
if [ ${p_id}x == ""x ]
then
	echo "应用${app_name}不在运行中"
	read -p "请选择操作编号: 1-运行/0-退出 : " my_choose
	if [ ${my_choose}x == "1"x ]
	then
		rm -rf nohup.out
		nohup ${java_jar} ${app_name} &
		sleep 2
		new_pid=`ps -ef | grep ${app_name} |grep -v grep | grep -v bash | awk '{print $2}'`
		echo "${app_name} 已运行，PID为 : ${new_pid}"
	fi
else
	echo "应用${app_name}正在运行中，PID为 : “${p_id}”"
	read -p "请选择操作编号: 1-停止/2-重启/0-退出 : " my_choose
	if [ ${my_choose}x == "1"x ]
	then
		kill -9 ${p_id}
		echo "${app_name} 已停止"
	elif [ ${my_choose}x == "2"x ]
	then
		kill -9 ${p_id}
		echo "${app_name} 已停止，正在重启中..."
		rm -rf nohup.out
		nohup ${java_jar} ${app_name} &
		sleep 2
		new_pid=`ps -ef | grep ${app_name} |grep -v grep | grep -v bash | awk '{print $2}'`
		echo "${app_name} 已重启，PID为 : ${new_pid}"
	fi
fi
exit
```

### 脚本demo

#### 字符串截取

```shell
string='http://www.quanchao.com/abc'
 #  # 左边开始，第一个
${string#*//}
 #  结果 : www.quanchao.com/abc

 #  ## 左边开始，最后一个
 ${var##*/}
  #  结果 : abc
  
#  % 右边开始，第一个
${string%/*}
#  结果 : http://www.quanchao.com

#  %% 右边开始，最后一个
${string%%/*}
 #  结果 : http:
```

#### 字符串包含

```shell
str_1="ssjt"
str_2="jt"
 
 
# 1、利用grep
result=$(echo $str_1 | grep "${str_2}")
if [[ "$result" != "" ]]
then
    echo "包含"
else
    echo "不包含"
fi
 
 
# 2、利用运算符
if [[ $str_1 =~ $str_2 ]]
then
    echo "包含"
else
    echo "不包含"
fi
 
 
# 3、利用通配符
if [[ $str_1 == *$str_2* ]]
then
    echo "包含"
else
    echo "不包含"
fi
 
 
# 4、利用替换
if [[ ${str_1/${str_2}//} == $str_1 ]]
    then
       echo "不包含"
    else
      echo "包含"
fi
```

## yum安装（CentOS、Fedora等）

### 命令指南

```shell
yum install package_name  # 安装指定软件包
yum update package_name   # 更新指定软件包
yum remove package_name   # 卸载指定软件包
yum search keyword        # 搜索指定关键字的软件包
yum list                  # 列出所有已安装的软件包
yum info package_name     # 查看指定软件包的详细信息
```

### 常用依赖下载

```shell
# 安装 gcc
yum install gcc
# 安装 readline
yum install readline-devel
# 安装 zlib
yum install zlib-devel
```

## apt安装（Ubuntu、Debian等）

### 命令指南

```shell
sudo apt-get install package_name   # 安装指定软件包
sudo apt-get update                 # 更新可用的软件包列表
sudo sudo apt-get upgrade           # 更新所有已安装软件包
sudo apt-get remove package_name    # 卸载指定软件包
sudo sudo apt-cache search keyword  # 搜索指定关键字的软件包
sudo apt list                       # 列出所有已安装的软件包
sudo apt-cache show package_name    # 查看指定软件包的详细信息
```

### 常用依赖下载

```shell
# 更新软件源
sudo apt-get update

# 安装 vim
sudo apt-get install vim
# 安装 ssh
sudo apt-get install openssh-client openssh-server
sudo  /etc/init.d/ssh restart
# 安装 ifconfig工具
sudo apt-get install net-tools
# 安装 GCC 这个命令会安装GCC编译器和其他一些必要的编译工具和库
sudo apt-get install build-essential
# 安装 readline
sudo apt-get install libreadline-dev libreadline6-dev
# 安装 zlib
sudo apt-get install zlib1g-dev 
sudo apt-get install libx32z1-dev lib32z1
#  安装 make
sudo apt-get install make
#安装 libaio.so
apt-get install libaio1
apt-get install libtinfo5

apt-get install pkg-config
```

## 其他命令

#### 用户

```shell
# 创建用户
adduser -r -c 'git version control' -d /home/git -m git
adduser -g root -d /app -m -r -c 'quanchao sys' quanchao 
# 如果只是运行useradd [username]的话，它会创建一个三无用户，即：无用户家目录，无指定shell版本，无密码。
-c<备注>：加上备注文字。备注文字会保存在passwd的备注栏位中；
-d<登入目录>：指定用户登入时的启始目录；
-m：自动建立用户的登入目录，如果指定目录不存在时，则自动创建。
-s：指定用户登入后所使用的shell，如果不设定的话，它的默认版本是/bin/sh
-D：变更预设值；
-e<有效期限>：指定帐号的有效期限；
-f<缓冲天数>：指定在密码过期后多少天即关闭该帐号；
-g<群组>：指定用户所属的群组；
-G<群组>：指定用户所属的附加群组；
-M：不要自动建立用户的登入目录；
-n：取消建立以用户名称为名的群组；
-r：建立系统帐号；
-u：指定用户id。

# 修改密码
passwd [username]

#查看用户列表
cat /etc/passwd
compgen -u
#查看组
compgen -g
```

#### 统计

```shell
wc									统计命令
	-c或–bytes：统计字节数
	-m或–chars：统计字符数
	-l或–lines：统计行数
	-w或–words：统计字数
-r 			-recursive				就是向下级目录递归
-f			-force					直接强行操作，不提示
-v			-verbose				详细显示进行的步骤
-i			-interactive			进行交互式地删除，操作文件前需要确认
-n									不覆盖已存在的文件
-u	 								移动或更改文件名时，若目标文件已存在，且文件日期比源文件新，则不覆盖目标文件


du -sh * 查看根目录下每个文件夹的大小
du -ah --max-depth=1
du -sh
du -lh --max-depth=1 
```

#### 授权

```shell
# 将文件或者文件夹指定用户组以及用户
chown -R postgres:postgres /usr/local/install/pgsql
# 授权
chmod 777 /usr/local/install/pgsql
```

#### 查看IP

```shell
[root@localhost /]# curl ipconfig.co
```

#### 环境变量

```shell
[root@localhost /]# vim /etc/profile

export GIT_HOME=/data/app/git/git-2.32.0
export JAVA_HOME=/data/app/jdk/jdk1.8.0_301
export CLASSPATH=$:CLASSPATH:$JAVA_HOME/lib/
export PGSQL_PATH=/usr/pgsql-11
export PATH=$PATH:$JAVA_HOME/bin:$PATH:$GIT_HOME/bin:$PATH:$PGSQL_PATH/bin
export TIME_STYLE='+%Y-%m-%d %H:%M:%S'

[root@localhost /]#  source /etc/profile
```

#### 时区

```shell
# 1.查看当前服务器时区&列出时区并设置时区（如已是正确时区，请略过）
[root@localhost /]# timedatectl
[root@localhost /]# timedatectl list-timezones
[root@localhost /]# timedatectl set-timezone Asia/Shanghai
```

#### 时间

```shell
#查询当前系统时间
[root@localhost /]# date
2015年 06月 03日 星期三 11:21:05 CST
#设置系统时间
[root@localhost /]# date -s "2021-09-14 14:47:00"
2021年 09月 14日 星期二 14:47:00 CST
#对操作保存修改
[root@localhost /]# hwclock --systohc
```

#### 日期格式

```shell
[root@localhost /]# vim  /etc/profile

export TIME_STYLE='+%Y-%m-%d %H:%M:%S'

[root@localhost /]# source /etc/profile
```

#### 防火墙

```shell
# firewall防火墙
[root@localhost /]# systemctl status firewalld
# 查看转态
[root@localhost /]# firewall-cmd --state
# 开启
[root@localhost /]# service firewalld start
# 重启
[root@localhost /]# service firewalld restart
# 关闭
[root@localhost /]# service firewalld stop
#注意分清楚linux的版本 命令会有所不同
#查看防火墙规则
[root@localhost /]# firewall-cmd --list-all 
```

#### 服务自启动

```shell
# 打开服务:
sudo systemctl start foobar
#关闭服务:
sudo systemctl stop foobar
#重启服务:
sudo systemctl restart foobar
#不中断正常功能下重新加载服务:
sudo systemctl reload foobar
#设置服务的开机自启动:
sudo systemctl enable foobar
#关闭服务的开机自启动:
sudo systemctl disable foobar
#查看活跃的单元:
systemctl list-units
#查看某个服务的状态:
systemctl status foobar
#查看已启动的服务列表：
systemctl list-unit-files|grep enabled
#查看启动失败的服务列表：
systemctl --failed
```

#### 压缩及解压

```shell
.tar
解包：tar xvf FileName.tar
打包：tar cvf FileName.tar DirName
(注：tar是打包，不是压缩！)
——————————————
.gz
解压 1：gunzip FileName.gz
解压2：gzip -d FileName.gz
压缩：gzip FileName
———————————————
.tar.gz 和 .tgz
解压：tar zxvf FileName.tar.gz
压缩：tar zcvf FileName.tar.gz DirName
———————————————
.bz2
解压1：bzip2 -d FileName.bz2
解压2：bunzip2 FileName.bz2
压缩： bzip2 -z FileName
———————————————
.tar.bz2
解压：tar jxvf FileName.tar.bz2        或tar --bzip xvf FileName.tar.bz2
压缩：tar jcvf FileName.tar.bz2 DirName
———————————————
.bz
解压1：bzip2 -d FileName.bz
解压2：bunzip2 FileName.bz
压缩：未知
———————————————
.tar.bz
解压：tar jxvf FileName.tar.bz
压缩：未知
———————————————
.Z
解压：uncompress FileName.Z
压缩：compress FileName
———————————————
.tar.Z
解压：tar Zxvf FileName.tar.Z
压缩：tar Zcvf FileName.tar.Z DirName
———————————————
.zip
解压：unzip FileName.zip
压缩：zip FileName.zip DirName
压缩一个目录使用 -r 参数，-r 递归。例： $ zip -r FileName.zip DirName
———————————————
.rar
解压：rar x FileName.rar
压缩：rar a FileName.rar DirName
———————————————
.lha
解压：lha -e FileName.lha
压缩：lha -a FileName.lha FileName
———————————————
.rpm
解包：rpm2cpio FileName.rpm | cpio -div
———————————————
.deb
解包：ar p FileName.deb data.tar.gz | tar zxf -
```

```shell
# 清空文件内容
cat /dev/null > app.sh
# 获取pid
ps -ef | grep xxxxx |grep -v grep | grep -v bash | awk '{print $2}'
```

