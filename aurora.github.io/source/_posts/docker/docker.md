---
title: docker使用
categories: 虚拟机相关
tags: 
	- docker
---

# Docker

## 一、简介

### 背景

在传统项目中，开发和运维的对接往往会出现环境不一致的问题，即使能够统一环境，也需要进行很多配置，非常麻烦。

所以人们就提出一种方式，即

> 把开发时的环境抽离并保存下来，当其他地方需要运行项目时，直接拉取这个环境，就省去了麻烦的配置过程。

这也就是docker的思想。

### 特点

>  docker采用容器的思想，把镜像配置在容器中，当其他人需要运行项目时，只需要从docker仓库中拉去相应的镜像，即可解决环境冲突问题。

> 轻巧，和VM相比，他只需要几兆空间，因为只用到了核心命令等

## 二、安装

> 官网：https://docs.docker.com/reference/

### 1.检查环境

```bash
uname -r              # 查看内核版本，需要3.1及以上
cat /etc/os-release   # 查看CentOS版本，需要7以上
```

### 2.安装

```bash
# 1.install 
yum install -y yum-utils

# 2.set repository  not sure
yum-config-manager \
--add-repo \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 

# 3.update index
yum makecache fast

# 4.install docker-ce
yum install docker-ce docker-ce-cli containerd.io

# 5.start docker
systemctl start docker
# sure
docker version


```

## 三、docker组件介绍

**docker的核心三大组件为：镜像、容器和仓库**

三者的关系如下所示：

<img src="https://gitee.com/aurora1004/pictures/raw/master/docker_component.png" alt="docker_component" style="zoom: 67%;" />

在谈这些之前，我们先要了解下Linux相关的一些基础知识。

Linux操作系统分为两部分，**内核与用户空间**。Linux真正**核心**部分在于内核，而**用户空间**只是各大厂商在内核基础上添加自己的软件与工具集形成的发布版本。



**镜像**

> docker镜像实际上是由一层一层的系统文件组成，这种层级的文件系统被称为UnionFS( Union file system  统一文件系统)，镜像可以基于dockerfile构建，dockerfile是一个描述文件，里面包含了若干条密令，每条命令都会对基础文件系统创建新的层次结构。

**镜像仓库**

镜像仓库顾名思义便是用来存放镜像的，docker提供了一个注册服务器（register）来保存多个仓库，每个仓库又可以包含多个具备不同tag的镜像，docker运作中使用的默认仓库是docker hub公共仓库。

**容器**

docker容器是由docker镜像创建的运行实例。

docker容器类似虚拟机，可以执行包含启动，停止，删除等。每个容器间是相互隔离的。

容器中会运行特定的运用，包含特定应用的代码及所需的依赖文件，因此可以看作是一个简易版的Linux环境（包含root用户权限，进程空间，用户空间和网络空间等）和运行在其中的应用程序。

## 四、常用命令

### 1.帮助命令

```bash
docker version
docker info    # 显示系统信息，包括容器和镜像
docker 命令 --help
```

### 2.镜像命令

##### 查看镜像

```bash
# 查看本地主机上的所有镜像
docker images  

# 可选项
	- a # all
	- q # 只显示镜像id
```

##### 搜索镜像

```bash
# 在docker hub中搜索镜像
docker search mysql

# 可选项
	--filter	# 搜索过滤 如 --filter=STARS=3000
```

##### 下载镜像

```bash
docker pull mysql
# 拉取结果分析，待补充

# 指定版本，注意，版本必须是存在的
docker pull mysql:5.7
```

##### 删除镜像

```bash
# 全部删除
docker rmi -f $(docker images -aq)

# 指定id或名称
docker rmi -f id或name

```

### 3.容器命令

首先，需要一个镜像才能运行容器。

##### 拉取cent OS镜像

```
docker pull centOS
```

##### 创建并启动容器

```bash
docker run [可选参数] image
# 参数
 --name="Name" 用来区分容器
 -d 后台运行
 -it 启动并进入容器
 -p 绑定端口，有多种方式
 	-p ip:主机端口:容器端口
 	-p 主机端口:容器端口(最常用)
 	-p 容器端口
 	容器端口
 -P 随机指定端口 
```

##### 查看运行的容器

```bash
# 查看正在运行的容器
docker ps
# 查看正在运行和历史运行的容器
docker ps -a
# 只显示id
docker ps -q
```

##### 退出容器

```bash
# 如果在创建容器时加上了 -it，就会直接进入到容器中

# 1.退出，但是容器也会停止
exit

# 2.退出，容器不停止
ctrl + p + q
```

##### 删除容器

```bash
docker rm 容器id

docker rm -f $(docker ps -aq)

docker ps -aq | xargs docker rm
```

##### 启动和停止容器

```bash
docker start   # 启动
docker stop    # 停止
docker kill    # 强制停止
docker restart # 重启
```

### 4.其他命令

##### 后台启动容器

```bash
docker run -d 镜像名
# 发现容器停止了
# 因为docker必须要有一个前台进程运行着才行
```

##### 查看日志

```bash
# 查看指定id的10条日志
docker logs -tf --tail 10 容器id

```

##### 查看容器中进程信息

```bash
docker top 容器id
```

##### 查看镜像元数据

```bash
docker inspect 容器id
```

