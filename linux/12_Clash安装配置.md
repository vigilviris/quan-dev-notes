### 下载安装

#### 下载

https://github.com/Dreamacro/clash/releases/download/v1.2.0/clash-linux-amd64-v1.2.0.gz

#### 安装

```shell
# 解压、重命名、授权
gzip -d ./clash-linux-amd64-v1.20.0.gz
mv clash-linux-amd64-v1.20.0 clash
chmod +x clash
# 首次执行初始化，自动生成config.yaml 和Country.mmdb两个文件
./clash
# 查找配置文件路径
find -name Country.mmdb
```

### 修改配置

#### Wallless自动生成配置文件

```shell
https://sub.wl-sub1.com/api/v1/client/subscribe?token=60b37231af6041ba82a7fce3457a81c8&flag=clash
将保存的文件修改为config.yaml并替换原配置文件
```

### 启动

#### systemd 服务

```shell
sudo vim /etc/systemd/system/clash.service

# 填入以下内容：
[Unit]
Description=Clash daemon, A rule-based proxy in Go.
After=network.target

[Service]
Type=simple
Restart=always
ExecStart=nohup /usr/local/install/vpn/clash -d /app/.config/clash & #前面为clash路径 后面接配置路径 

[Install]
WantedBy=multi-user.target
```

#### 使用 systemctl

```shell
sudo systemctl enable clash # 开机启动
sudo systemctl disable clash # 禁用开机启动
sudo systemctl start clash # 启动
sudo systemctl status clash # 查询状态
sudo journalctl -xe # 日志
```

### 设置代理

#### 永久

```shell
vim /etc/profile


export https_proxy=http://127.0.0.1:7890 
export http_proxy=http://127.0.0.1:7890 
export all_proxy=socks5://127.0.0.1:7890


source /etc/profile
```

#### 临时

```shell
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7890
# 取消：
unset http_proxy https_proxy all_proxy
```

