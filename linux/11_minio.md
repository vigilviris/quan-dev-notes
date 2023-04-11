### 下载

官网下载地址：https://min.io/download#/linux

### 安装

#### 创建安装目录

```shell
# 进入安装目录 install_dir以下用...代替表示
cd [install_dir]
# 创建相应文件夹
mkdir minio
mkdir minio/data minio/log
```

#### 上传安装包

将官网下载的安装包放到上一步创建的minio目录下

```shell
# 进入minio安装目录
cd .../minio
# 给予权限
chmod +x minio
```

#### 默认启动

```shell
MINIO_ACCESS_KEY=admin MINIO_SECRET_KEY=Init1234 nohup ./minio server .../minio/data > .../log/minio.log &
```

#### 自定义端口启动

```shell
MINIO_ACCESS_KEY=admin MINIO_SECRET_KEY=Init1234 nohup ./minio server --address :9000 --console-address :9001 /home/minio/data > /home/minio/data/minio.log &    
```

#### 查看日志

http://192.168.14.208:36067即mino控制台访问地址

```log
nohup: 忽略输入
WARNING: MINIO_ACCESS_KEY and MINIO_SECRET_KEY are deprecated.
         Please use MINIO_ROOT_USER and MINIO_ROOT_PASSWORD
Formatting 1st pool, 1 set(s), 1 drives per set.
WARNING: Host local has more than 0 drives of set. A host failure will result in data becoming unavailable.
MinIO Object Storage Server
Copyright: 2015-2023 MinIO, Inc.
License: GNU AGPLv3 <https://www.gnu.org/licenses/agpl-3.0.html>
Version: RELEASE.2023-03-13T19-46-17Z (go1.19.7 linux/amd64)

Status:         1 Online, 0 Offline. 
API: http://192.168.14.208:9000  http://127.0.0.1:9000     
Console: http://192.168.14.208:36067 http://127.0.0.1:36067   

Documentation: https://min.io/docs/minio/linux/index.html
```

