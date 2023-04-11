#### 安装GIT环境

```shell
yum -y install curl curl-devel zlib-devel openssl-devel perl cpio expat-devel gettext-devel gcc cc
```

#### 解压编译安装GIT

```shell
#解压缩
tar -zxf git-2.32.0.tar.gz
cd git-2.32.0
#配置
autoconf
./configure
#编译
make
make install
```

#### 创建用户

```shell
#创建git用户，以/home/git目录作为主目录
adduser -r -c 'git version control' -d /home/git -m git
passwd git
git123qc
```

#### 打开linux机器的RSA认证：

```shell
vim /etc/ssh/ssh_config

Host *
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys

# 重启sshd服务
systemctl restart sshd.service
```
#### SSH KEY

```shell
# 配置用户名和邮箱
git config --global user.name "用户名"
git config --global user.email "邮箱地址"

## 1、windows 生成ssh key
ssh-keygen -t rsa -C "邮箱地址"

## 2、windows 查看当前电脑的 ssh key
# 目录为 C:\Users\username\.ssh
cat ~/.ssh/id_rsa.pub
# windows的ssh key公钥
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDeAMx74ohXVpg25BgKwBBegFQVra90C2F64UjOnxz9jYCjwBCBkC7kjhY4wOAPDFjzhYvQMxR9LLA59il8FBRW/vwbetbUa1Sqcy3Bs0t/dykjG3bmHV8KCeKjisILyBYJvAr7YsQlGKjX68H2S0Nrlq7miE3WFhe+zlKF8T5CQ4cOqP5NtYxmwTK4NVNLEKXf4yHhBlEVimuL+hNBp876R8yF6sldjUzBl5M2pWVJsf21TCC7clMrge67YgHwg7Re+YzMaOkWSu87b1o6Qb/ydS1nTmpBra/5PROCuffZikm03uDNdqx0OGoIED6ss3U

## 3、linux 新建.ssh目录
mkdir /home/git/.ssh
## 4、linux配置公钥
# 方法一：编辑linux的authorized_keys 复制粘贴ssh key公钥
vim ~/.ssh/authorized_keys
# 方法一：上传文件win电脑的id_rsa.pub到linux的.ssh目录，然后生成authorized_keys
cat id_rsa.pub >> authorized_keys

## 4、修改 .ssh/authorized_keys 文件的权限为 600
chmod 600 /home/git/.ssh/authorized_keys
## 5、修改 .ssh 目录的权限为 700
chmod 700 /home/git/.ssh 
```

#### 限制git用户登录ssh

```shell
# 看git-shell是否存在
cat /etc/shells 
# 查看git-shell路径
which git-shell
# 添加git-shell路径到/etc/shells 文件中
# 限制git用户登录 git用户可以正常通过ssh使用 git，但无法通过ssh登录系统
vim /etc/passwd
git:x:994:991:git version control:/home/git:/bin/git-shell
```

#### 初始化代码仓

```shell
#初始化git仓并root授权
mkdir /app/git
chown -R git:git /app/git/
chmod 777 /app/git/

# 初始化项目代码仓
cd /app/git
git init --bare xxx-xxx.git
# 修改代码仓所有者为git，目的是git用户
chown -R git:git xxx-xxx.git

# 本地代码首次提交到远程
git init
git add .
git commit -m "init commit"
git remote add github [git代码仓ssh地址]
# 推送提交代码到main分支
git push -u github main
```

#### GIT命令

```shell
# 远程代码仓操作
# 查看
git remote -v
# 重命名
git remote rename origin [remote_name]
# 删除
git remote rm [remote_name]
# 新增
git remote add [remote_name] git@xxx.xxx.xxx.xxx:/xxx/xxx/xxx-xxx.git
# 重置
git remote set-url [remote_name] ssh://git@xxx.xxx.xxx.xxx:/xxx/xxx/xxx-xxx.git
# 提交
git push -u aliyun --all
git push -u [remote_name] --tags
git push [remote_name] [branch_name]
# 把本地的 localhost 分支强制(-f)推送到远程 master
git push [remote_name] localhost:[branch_name] -f

# 查看本地分支
git branch
# 查看远程分支:
git branch -r
# 查看全部分支:
git branch -a
# 创建本地分支
git branch [branch_name]
# 删除本地分支
git branch -D [branch_name]
git branch -d 会在删除前检查merge状态（其与上游分支或者与head）。
git branch -D 是git branch --delete --force的简写，它会直接删除。
# 删除远程分支
git push [remote_name] --delete [branch_name]
# 清理远程分支
git remote prune [remote_name]
# 清理无效分支(远程已删除本地没删除的分支): 
git fetch -p
# 查看远程可被清理的分支
git remote prune [remote_name] --dry
# 模糊查找分支: 
git branch | grep 'remote_name'
# 查看本地分支和追踪情况
git remote show origin
```

