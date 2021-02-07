---
sidebarDepth: 3
---

# Docker速学

Docker 需要重点掌握的内容包括：

* 核心原理
* 基础三大件：镜像、容器、仓库
* 镜像生产：通过 Dockerfile 编排镜像所需的资源
* 持久化存储：将容器的数据保存在宿主机磁盘中
* 端口与互联：容器与宿主机、容器与容器、容器与外部的连接与通信机制
* 用户权限：容器中的用户与宿主机的用户之间的关系
* 编排：通过多容器互联，实现所需的业务场景

## 概念

Docker 是利用 Linux 的虚拟隔离技术（namespace）将操作系统分割为多个子操作系统，子操作系统之间互不干扰的一种虚拟化技术。

为什么会出现 Docker 技术？ 其实主要有两个原因：

* 软件异构成为一种主流，一个应用需要多个虚拟机的情况已经非常普遍
* 传统的操作系统笨重（占用 10G 左右的存储空间）、启动速度太慢（大约 20s）

也就是说，现在的系统架构，要求需要大量运行速度极快，资源占用甚少的轻量级虚拟机。  

Docker 的出现，正好解决了上述的困扰，轻量化的虚拟机改变了系统架构，云原生诞生于此。

## 核心原理

Docker 的核心原理可以归纳为两点：虚拟文件系统+虚拟用户。

什么意思呢？ 

虚拟文件系统本质上还是宿主机上的文件，但通过虚拟技术就变成了一种“独占”资源。  
虚拟用户本质山还是宿主机上的用户，但通过虚拟技术让容器认为自己有了单独的用户管理。  

仅有虚拟还不够，在技术上必须有隔离方可确保容器之间互不干扰。  

Docker 的原理实际上是人类管理思想的技术落地：对已有的资源进行虚拟组合，形成一种新的有边界的权利机构。  

再回过头来澄清两个概念：

* **宿主机**：运行 Docker 系统的那台虚拟机，被称之为宿主机
* **容器**：通过 Docker 创建的轻量级虚拟机，被成为容器（Container）

容器与宿主机站在虚拟机的角度说，它们不是从属关系，而是并列关系--这是了解 Docker 的关键。  

容器虚拟机拥有传统虚拟机的一切功能和操作方式，只有这样才能跳出“技术陷阱”，直接借鉴虚拟机的技术原理来掌握容器技术。

容器存在的根本目前也是用于运行软件，既然是运行软件，同样需要：

* 安装基础环境
* 与外部互联
* 被外界访问
* 存储数据
* 存储代码
* 高可用
* 负载均衡

## 镜像

对于 Docker 来说，镜像（Image）就是一个打包好了的文件包，这个文件包可以直接在 Docker 中运行，运行后的状态就是容器（Container）

> 例如：MySQL 镜像= 虚拟的 Linux 操作系统 + MySQL

故，一个镜像在部署的时候，是需要用户给可配置的参数赋值的。那这些参数在哪里？怎么设置？这个需要查看MySQL镜像对应的技术文档。

#### 镜像是怎么产生的？

用户编写构建镜像的配置文件 DockerFile，对这种文件进行 build 操作，就生成了一个镜像。

#### 镜像是一个文件？

镜像从逻辑上可以简单理解是一个文件，但实际上是多层文件的组合。

镜像虽然不是一个大文件，但可以被导出成一个大文件：

```
# 镜像导出成一个tarball文件
docker save image

# 加载一个 tarball 镜像文件
docker load image
```

#### 镜像存放在哪里？

镜像仓库中。Docker 公司的 Dockerhub 是全球最大的镜像仓库。  

也支持自建仓库，云提供商（阿里云、华为云等）也会提供镜像仓库服务，供客户存放自己的私有镜像。

## 容器

容器逻辑上基于 Docker 的一个正在运行的虚拟机，实际上是共享操作系统内核的独立进程。 

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/container-what-is-container.png)

容器的内核有一部分共享的镜像的不变文件，另外一部分是可变文件。  

所以，容器也可以进行导出和导入操作

```
# 将容易导出成 tarball 文件
docker export

# 加载一个 tarball 文件为容器
docker import
```

> 从功能上讲，docker export相当于commit ＋save，先将容器commit成镜像，再save成文件。

## 持久化存储

由于容器是运行时，那么用户一定会思考一个问题：“容器运行过程产生了一些需要长期保存的数据，一旦停止或销毁，所有产生的数据就会消失。怎么办？” 以运行WordPress为例，WordPress的wp-content是需要保留数据库的文件夹，如果在容器下运行，如何保存呢？

其实Docker考虑这种场景，Docker可以给容器“挂载”一个固定的外部存储空间，这个存储空间是服务器操作系统来管理的。

以WordPress为例，Docker处理长期数据保存的机制如下：

1. 容器运行之前，给wp-content映射到一个Docker之外的固定存储路径
2. 运行容器，wp-content的数据就会保存下来


## 网络和端口

容器不是用来看的，是需要被外界访问或其他应用程序调用的，理解容器的网络机制就很有必要的。

#### 内网地址

对于Docker系统来说，默认有一个容器路由功能，简单的说，Docker会给每个部署好的Container生成一个内网IP地址。例如，Docker下运行了容器，Docker就自动分配了3个内网地址：
```
容器1 172.18.0.1
容器2 172.18.0.2
容器3 172.18.0.23
```
对于其中任何Container来说，都可以通过IP地址作为访问通道

#### 端口

每个Container，都可以映射到服务器的一个端口上，以便于外部访问这个Container。
例如：172.18.0.1 上运行了MySQL，且MySQL本身开启了外部访问。这个时候，如何通过服务器的IP地址来访问这个MySQL呢？
1. 首先，将容器1的做一个端口映射，加入映射到都服务器的3306端口

2. 然后，通过 服务器IP:3306 就可以访问MySQL

问题：Container中的应用为什么有端口号？Container是带最简的操作系统的，有操作系统就一定会通过端口访问程序

## 用户权限

虽然有用户名的概念，但由于 Linux 内核最终管理的用户对象是以 uid 为标识，所以本节均以 uid 来替代用户名。  

默认情况下，容器中的进程以 root 用户权限运行，并且这个 root 用户和宿主机中的 root 是同一个用户。  

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/docker-uidgid-websoft9.png)

容器由于是基于虚拟隔离技术的并共享操作系统内核的独立进程，而内核只管理一套 uid 和 gid，所以容器中的 uid 和 gid 实际上与宿主机内核是一套体系。

> 理解容器中用户权限、uid、gid 等本质，重点在于理解 《Linux User Namespace》

当容器进程尝试写入文件时，内核会检查此容器的 uid 和 gid，以确定其是否具有足够的特权来修改文件。

## 编排

编排就是将多个运行的容器串联起来，最常见的编排工具是 Docker Compose。下面是 OnlyOffice 安装所用的 docker-compose 配置文件：

```
version: '2'
services:
  onlyoffice-communityserver:
    container_name: onlyoffice-communityserver
    image: onlyoffice/communityserver:latest
    environment:
      - MYSQL_SERVER_HOST="127.0.0.1"
      - MYSQL_SERVER_PORT="3306"
      - MYSQL_SERVER_DB_NAME="onlyoffice"
      - MYSQL_SERVER_USER="onlyoffice"
      - MYSQL_SERVER_PASS="123456"
      - DOCUMENT_SERVER_PORT_80_TCP_ADDR=onlyoffice-documentserver
    stdin_open: true
    restart: always
    networks:
      - onlyoffice
    volumes:
      - /data/wwwroot/onlyoffice/CommunityServer/logs:/var/log/onlyoffice
      - /data/wwwroot/onlyoffice/CommunityServer/data:/var/www/onlyoffice/Data
      - /data/wwwroot/onlyoffice/CommunityServer/DocumentServer/data:/var/www/onlyoffice/DocumentServerData
    ports:
      - '80:80'
      - '443:443'
      - '5222:5222'
       
  onlyoffice-documentserver:
    container_name: onlyoffice-documentserver
    image: onlyoffice/documentserver:latest
    stdin_open: true
    restart: always
    networks:
      - onlyoffice
    ports:
      - '9002:80'
      - '9003:443'
    volumes:
      - /data/wwwroot/onlyoffice/DocumentServer/logs:/var/log/onlyoffice
      - /data/wwwroot/onlyoffice/DocumentServer/data:/var/www/onlyoffice/Data

networks:
  onlyoffice:
    driver: 'bridge'
```
注意：
1. volumes 设置中的前面部分是宿主机的目录，后面是容器的目录，宿主机的目录无需提前创建
2. 容器可以很方便的连接宿主机创建的数据库，上面的onlyoffice-communityserver中的MySQL连接配置就是宿主机连接范例

## 常见命令

下面是使用Docker可能需要用到的常见命令

~~~
systemctl start/stop docker     运行/停止 docker 服务
systemctl enable docker         使 docker 开机自启
docker pull                     从镜像库拉取容器镜像
docker ps                       查看正在运行的容器列表（可以看到容器ID，所映射的端口号等等）
docker ps -a                    查看所有的容器（不管是否运行都能看到）
docker start/stop CONTAINER ID  开始/停止容器（CONTAINER ID 是容器的ID）            
docker rm CONTAINER ID          删除容器
docker stop $(docker ps -aq)    停止所有容器
docker rm $(docker ps -aq)      删除所有容器
docker kill CONTAINER ID        直接关闭容器
docker rmi $(docker images -q) -f 删除所有镜像
docker images  # 查询已下载镜像
~~~