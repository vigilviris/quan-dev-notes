# 介绍

**官方文档**https://go-zero.dev/cn/docs/introduction

## go-zero

晓黑板golang开源项目，集各种工程实践于一身的web和rpc框架。

## goctl

一个旨在为开发人员提高工程效率、降低出错率的辅助工具。可以根据定义的 api 文件一键生成 Go, iOS, Android, Kotlin, Dart, TypeScript, JavaScript 代码，并可直接运行。

# 安装

```
go get -u github.com/zeromicro/go-zero
```

# 环境准备

## goctl安装

### go install安装

```go
go install github.com/zeromicro/go-zero/tools/goctl@latest
```

### 验证

```go
goctl -v
goctl version 1.4.4 windows/amd64
```

## proto&protoc-gen-go安装

### 方法一：goctl一键安装

**此方法可以同时安装protoc和protoc-gen-go**

```go
goctl env check -i -f --verbose

[goctl-env]: preparing to check env

[goctl-env]: looking up "protoc"          
[goctl-env]: "protoc" is not found in PATH
[goctl-env]: "protoc" is already installed in "D:\\Go\\go 1.18.1\\bin\\protoc.exe"
[goctl-env]: looking up "protoc-gen-go"
[goctl-env]: "protoc-gen-go" is not found in PATH
[goctl-env]: preparing to install "protoc-gen-go"
write cache error: open D:\Go\go 1.18.1\bin\protoc-gen-go.exe: The system cannot find the file specified.
[goctl-env]: "protoc-gen-go" is already installed in "D:\\Go\\go 1.18.1\\bin\\protoc-gen-go.exe"

[goctl-env]: looking up "protoc-gen-go-grpc"
[goctl-env]: "protoc-gen-go-grpc" is installed

[goctl-env]: congratulations! your goctl environment is ready!

```

### 方法二：源文件安装

#### 安装protoc

1、源文件下载：https://github.com/protocolbuffers/protobuf/releases/tag/v21.12

2、解压protoc-21.12-win64.zip，然后将protoc-21.12-win64\bin\protoc.exe文件移动至{$GOPATH}/bin

#### 安装protoc-gen-go

```go
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```

### 验证

```
$ protoc --version
libprotoc x.x.x
```

# 开始



