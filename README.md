# Islands


**这是一个团队项目，这个仓库仅作为展示页面，我主要负责对底层 runtime 的操控（使用 runc/crun 创建管理容器、容器的网络设置、容器的日志）以及镜像的拉取等一些功能的实现，下面的文档用于展示这个项目的结构**

一个轻量级的容器管理引擎


## 概述

一个轻量级的容器引擎，仅提供最基本的容器服务。

外部程序通过gRPC接口对镜像、卷、容器等进行操作


## 使用方法

编译本项目前需要先安装依赖

- go 1.13+
- make

如果需要编译proto文件生成服务端和客户端代码，则还需要安装以下依赖

- protoc
- protoc-gen-go

使用makefile可以编译整个项目

- make 编译，运行islands
- make cmd 编译islands-cli
- make build 编译整个项目
- make proto 根据protobuf文件生成go代码
- make clean 清理编译生成的可执行文件和protobuf生成代码
- make help  显示帮助信息

## 项目组件

- island —— 容器Runtime管理器
- lode   —— 镜像管理器
- guardian —— 守护人
- cmd    —— 命令行工具

## island —— 容器Runtime管理器

目前使用runc作为容器Runtime

使用方式如下：

+ 在这个页面下载合适的 [Binary](https://github.com/opencontainers/runc/releases) 或者代码自己编译生成 Binary。比如从这个页面下载 runc.amd64 并改名成 runc（这个 Binary 的名字一定要改成 runc）
+ 将这个 runc 放在 /usr/bin 目录下面
+ 在终端中输入 `runc -h` 检查是否成功



## lode   —— 镜像管理器

本地镜像仓库，已支持导入docker save格式的镜像

本地镜像仓库的目录结构:
<pre>
RootPath(根目录)
    ├── imagedb（镜像仓库信息）
    │   ├── d87d8c9adef5859039a5ab84e023111512970d8006ca4684c8d03122975b40f0.json（某个镜像信息）
    │   └── images.json（整个镜像仓库所存）
    ├── mnt（用来挂载各个镜像）
    │   └── d87d8c9adef5859039a5ab84e023111512970d8006ca4684c8d03122975b40f0（要挂载的镜像id）
    │       ├── merge（该镜像挂载点）
    │       └── work（overlayFS的工作workdir）
    └── layerdb（存放层的仓库）
        ├── 1d0dfb259f6a31f95efcba61f0a3afa318448890610c7d9a64dc4e95f9add843（存放某层数据的文件夹）
        └── layers.json（整个层仓库的信息）
</pre>

## guardian —— 守护人

控制器和daemon，负责容器生命周期的管理

## cmd    —— 命令行工具

用于控制和使用islands的命令行工具

如果修改了islands的api地址，则需要在运行前通过环境变量进行指定，如

`export ISLANDS_API=unix://islands.sock`

### 支持命令

- islands run
- islands start
- islands image
- islands attach

### islands image

镜像管理

#### islands image list
#### islands image import <path>
#### islands image remove <id>
#### islands image clean

## 使用到的开源项目

| 项目 | 协议 |说明 |
|--|--|--|
| [logrus](https://github.com/sirupsen/logrus) | MIT | 日志库 |
| [logrus-prefixed-formatter](https://github.com/x-cray/logrus-prefixed-formatter) | MIT | 用于logrus的带前缀的格式生成器 |
| [runc](https://github.com/opencontainers/runc)| Apache-2.0 | 根据OCI规范实现的容器生成与运行工具 |
| [go-runc](https://github.com/containerd/go-runc) | Apache-2.0 | runc的go语言绑定