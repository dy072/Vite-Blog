---
title: Docker 入门使用
head:
  - - meta
    - name: keywords
      content: Docker 虚拟化
  - - meta
    - name: description
      content: Docker 虚拟化
---

## Docker 技术简介

Docker 如日中天，这不是单纯的炒的概念，Docker 的确解决了开发和运费的痛点，比如版本的搬迁，从测试环境搬迁到预发、生产环境，在企业开发与测试中，得到了非常广泛的应用。

- Docker 是一个开放源码的软件项目，基于 GO 语言
- Docker 可以让开发打包他们的应用以及依赖包到一个轻量级、可移植的容器中
- Docker 打包好容器，可以发布到任何流行的 Linux 机器上，也可以实现虚拟化
- 容器是完全使用沙箱机制，互相之间不会有任何接口
- Docker 容器性能开销极低，这个才是关键
- Docker 使用客户端-服务器（C/S）架构模式，使用远程 API 来管理和创建 Docker 容器

## Docker 的应用场景

1.  Web 应用自动化打包和发布
2.  自动化测试和持续集成、发布
3.  在服务器环境中部署和调整数据库或其他的后台应用
4.  使用 Docker 可以实现开发环境、测试环境、生成环境的一致性

Docker 借鉴了标准的集装箱的概念。标准的集装箱将货物运往世界各地，Docker 将这个模型运行到自己的设计中。唯一不同的是: 集装箱是运输货物，Docker 是运输软件。

**Docker 和传统的 VM 的区别**

docker 与传统的 VM 对比:

1.  启动速度：vm 一般几十秒或者几分钟，docker 几秒或者毫秒
2.  大小：vm 一般几 G，docker 只有几兆

## Docker 的组成

Docker 需要分为三部分： `仓库`、 `镜像` `容器`

|                 |                                                                                                                                                          |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Docker 镜像     | Docker 镜像是用于创建 Docker 容器的模板。镜像是基于联合文件系统的一种层式结构，由一系列指令一步一步构建出来。                                            |
| Docker 容器     | 容器是独立运行的一个或多个应用。镜像相当于类，容易相当于类的实例                                                                                         |
| Docker 客户端   | Docker 客户端通过命令行或者其他工具使用 Docker API 与 Docker 的守护进程通信                                                                              |
| Docker 主机     | 一个物理或者虚拟的机器用于执行 Docker 守护进程和容器                                                                                                     |
| Docker 守护进程 | 是 Docker 服务器端进程，负责支撑 Docker 容器的运行以及镜像的管理                                                                                         |
| Docker 仓库     | Docker 仓库用来保存镜像，可以理解为代码控制中的代码仓库。Docker Hub 提供了庞大的镜像集合供使用，用户也可以将自己本地的镜像推送到 Docker 仓库供其他人下载 |

### docker 进程相关操作

**查看 docker 版本**

```bash
docker -v
```

**查看 docker 命令**

```bash
docker --help
```

**查看 docker 服务端与客户端版本详情**

```bash
docker version
```

**启动 docker**

```bash
systemctl start docker
```

**关闭 docker**

```bash
systemctl stop docker
```

**重启 docker**

```bash
systemctl restart docker
```

**查看 docker 运行状态**

```bash
systemctl status docker
```

**关闭防火墙**

```bash
systemctl stop firewalld
```

### docker 加速

默认情况下，docker 下载镜像是从官网下载，下载速度特别慢，我们可以使用阿里云加速器可已提升获取 Docker 官网镜像的速度。

1.  **执行目录创建文件（如已存在请忽略此步）**

```bash
touch /ect/docker/deamon.json
```

2.  **修改文件内容为**

```cpp
   {
     "registry-mirrors": ["https://v2c6fjn8.mirror.aliyuncs.com"]
   }
```

注意：此网址是从阿里云控制台复制过来的，每个登录用户都不一样。当然用这个也是可以的。

3.  **重启 docker 服务**

```bash
systemctl restart docker
```

### 镜像操作

**查看本地镜像**

```bash
docker images
```

**搜索镜像，这里以安装 `tomcat` 为例**

<<< @/code/docker.sh

**找到所有的镜像，拉取镜像**

```bash
docker pull 镜像ID  #最新版本 如： docker pull tomcat
docker pull tomcat:7 # 指定版本
```

**删除镜像**

```bash
docker rmi 容器名称
```

**本地以有镜像，创建容器**

```bash
docker create --name=容器名称 镜像名称
```

```bash
示例:
[root@iZbp18425116ezmjdmbdgeZ ~]# docker create --name=myTomcat tomcat
629104460e3fe485649e5784ec0c34f4dcc2277115937a54be00b968dc646510
```

**查看容器**

```bash
docker ps # 查看正在运行的容易
docker ps -a # 查看所有容器
```

**运行容器**

```bash
docker start myTomcat # start 后端可以跟容易id (前2-3位) 或者容器名称
```

**停止容器**

```bash
docker stop myTomcat
```

**删除没有运行的容器**

```bash
docker rm 容器id或者名称
```

**删除正在运行的容器**

```bash
docker -rm -f 容器id或者名称
```

**删除所有容器**

```bash
# 先停止所有的容器
docker stop $(docker ps -a -q)

# 删除
docker rm $(docker ps -a -q)
```

### 运行容器的相关操作

**查看 docker run 命令 ，创建并且启动容器**

```bash
docker run --help
docker run 后面的参数
	-i # 运行容器
	-t # 容器启动后，进入命令行
	-v # 目录映射 --挂载
	-d # 守护进程 --后台运行
	-p # 端口映射 如容器里面有tomcat，你本地的windows想访问:
	# docker tomcat 端口号是8080，需要在虚拟机映射一个端口9099
	# windows才可以访问 http://虚拟机ip:9099
```

**创建容器，并且进入命令行**

```bash
docker run -it --name=myTomcat2 tomcat /bin/bash
```

**退出**

```bash
exit
```

**退出之后，重新进入容器**

```bash
# 创建一个守护的容器
docker run -id --name=myTomcat2 tomcat
# 进入
docker exec -it myTomcat2 /bin/bash
```

**宿主机与 docker 容器的文件传递**

```bash
1、在宿主机上创建一个文件
touch test.txt

2、把文件复制到容器里面去
docker cp test.txt myTomcat2:/

3、进入跟目标
cd /
```

**从容器中的文件 copy 到宿主机中**

```bash
touch abc.txt

# 退出容器
exit

# 将容器中的文件复制到宿主机
docker cp myTomcat2:abc.txt /root
```

### 访问 tomcat

```bash
# 启动并作为守护进程
# -p 宿主机的端口: 容器里应用的端口8080
# war 挂载宿主机 -v 宿主机的路径: 容器路径
docker run -di --name --myTomcat -p 8080:8080 -v /usr/local/tomcat/webapps:/usr/local/tomcat/webapps tomcat

http://虚拟机ip:9999

 # 把war包放到宿主机的挂载目录中，直接刷新浏览器就出现项目的页面
# 微服务 k8s 容器式的性能测试
```

### 镜像备份

```bash
docker save -o tomcat.tar 镜像名称

# 把tomcat.tar 放到其他电脑，其他人变成镜像
docker load -i tomcat.tar
```

### 容器之间的关联

```bash
docker run -di --name=tomcat2 -p 8080:8080 --link mysql
```
