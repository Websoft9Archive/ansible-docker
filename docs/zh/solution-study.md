---
sidebarDepth: 3
---

# Docker速学

## 操作系统视角看Docker

先来看回顾操作系统的知识：

* 一个没有被虚拟化的物理机或一个虚拟机，都只能有一个操作系统存在。  
* 操作系统的启动需要30s以上，安装需要1h，备份需要10G以上的存储，整体来看，显得比较笨重
* 操作系统一方面与硬件资源打交道，一方面与上层应用打交道

Docker技术解决了以上的困扰，通过Docker技术，可以将一个操作系统虚拟成成百上千个无需与硬件资源打交道的虚拟操作系统（即容器）。

深入思考，Docker的原理实际上是人类管理思想的技术落地。 分层管理，分而治之。

Docker是用来运行应用程序的，而应用程序必须在操作系统上运行，这个基本原理是无法改变的。因此一个Docker实例（Container）是包含一个简易的Linux或Windows操作系统。   
> 可以说，Container=运行时操作系统+应用软件（含依赖组件）。如果从操作系统视角看Container就更加便于理解Container。

## 网络和端口

容器不是用来看的，是需要被外界访问或其他应用程序调用的，理解容器的网络机制就很有必要的。

### 内网地址

对于Docker系统来说，默认有一个容器路由功能，简单的说，Docker会给每个部署好的Container生成一个内网IP地址。例如，Docker下运行了容器，Docker就自动分配了3个内网地址：
```
容器1 172.18.0.1
容器2 172.18.0.2
容器3 172.18.0.23
```
对于其中任何Container来说，都可以通过IP地址作为访问通道

### 端口

每个Container，都可以映射到服务器的一个端口上，以便于外部访问这个Container。
例如：172.18.0.1 上运行了MySQL，且MySQL本身开启了外部访问。这个时候，如何通过服务器的IP地址来访问这个MySQL呢？
1. 首先，将容器1的做一个端口映射，加入映射到都服务器的3306端口

2. 然后，通过 服务器IP:3306 就可以访问MySQL

问题：Container中的应用为什么有端口号？Container是带最简的操作系统的，有操作系统就一定会通过端口访问程序

## 镜像

对于Docker来说，镜像（Image）就是一个打包好了的文件包，这个文件包可以直接在Docker中部署，部署之后就产生了一个容器（Container）

> 例如：MySQL镜像=Linux操作系统+MySQL软件+可设置的配置参数

故，一个镜像在部署的时候，是需要用户给可配置的参数赋值的。那这些参数在哪里？怎么设置？这个需要查看MySQL镜像对应的技术文档。

### 镜像是怎么制作出来的？

简单的说将DockerFile通过build命令跑一下，就生成了一个镜像

### 镜像是一个单独的大文件还是一序列文件集合？

镜像简单理解是一个文件夹，但也可以制作成压缩包

* docker save是将一个镜像导出成一个tarball文件，对应的导入命令是docker load，将该文件导入成一个镜像。
* docker export是将一个容器导出成一个tarball文件，对应的导入命令时docker import，将该文件导入成一个镜像（注意不是容器）。

> 从功能上讲，docker export相当于commit ＋save，先将容器commit成镜像，再save成文件。

### 镜像仓库

顾名思义，镜像仓库就是大量镜像集中存放的地方。最大的镜像仓库是Docker公司的Dockerhub，另外各个Docker主机提供商（阿里云、华为云等）也会提供镜像仓库服务，供客户存放自己的私有镜像。

## 数据固定存储 

由于容器是运行时，那么用户一定会思考一个问题：“容器运行过程产生了一些需要长期保存的数据，一旦停止或销毁，所有产生的数据就会消失。怎么办？” 以运行WordPress为例，WordPress的wp-content是需要保留数据库的文件夹，如果在容器下运行，如何保存呢？

其实Docker考虑这种场景，Docker可以给容器“挂载”一个固定的外部存储空间，这个存储空间是服务器操作系统来管理的。

以WordPress为例，Docker处理长期数据保存的机制如下：

1. 容器运行之前，给wp-content映射到一个Docker之外的固定存储路径
2. 运行容器，wp-content的数据就会保存下来

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
docker kill CONTAINER ID        直接关闭容器
docker images  # 查询已下载镜像
~~~

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
