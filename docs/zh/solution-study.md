---
sidebarDepth: 3
---

# Docker 速学

Docker 需要重点掌握的内容包括：

* 核心原理
* 基础三大件：镜像、容器、仓库
* 镜像生产：通过 Dockerfile 编排镜像所需的资源
* 持久化存储：将容器的数据保存在宿主机磁盘中
* 端口与互联：容器与宿主机、容器与容器、容器与外部的连接与通信机制
* 用户权限：容器中的用户与宿主机的用户之间的关系
* 编排：通过多容器互联，实现所需的业务场景

> [Docker 官方文档](https://docs.docker.com/)非常完善，而且非常有层次结构。

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/docker-documentation-websoft9.png)

## 概念

Docker 是利用 Linux 的虚拟隔离技术（namespace）将操作系统分割为多个子操作系统，子操作系统之间互不干扰的一种虚拟化技术。

为什么会出现 Docker 技术？ 其实主要有两个原因：

* 软件架构复杂化，一个应用需要多个虚拟机协作支撑的情况已经非常普遍（也可以理解为微服务化）
* 传统的操作系统笨重（占用 10G 左右的存储空间）、启动速度太慢（大约 20s）

也就是说，现在的系统架构，要求需要大量运行速度极快，资源占用甚少的**轻量级虚拟机**。  

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/docker-vs-vm.png)

Docker 的出现，正好解决了上述的困扰，轻量化的虚拟机改变了系统架构，云原生诞生于此。

## 核心原理

Docker 的核心原理可以归纳为两点：**虚拟文件系统+虚拟用户**。

什么意思呢？ 

* 虚拟文件系统本质上还是宿主机上的文件，但通过虚拟技术就变成了一种“独占”资源。  
* 虚拟用户本质山还是宿主机上的用户，但通过虚拟技术让容器认为自己有了单独的用户管理。  

仅有虚拟还不够，在技术上必须有隔离方可确保容器之间互不干扰。  

Docker 是对操作系统资源进行虚拟组合，形成一种新的有边界子操作系统。  

再回过头来澄清两个概念：

* **宿主机**：运行 Docker 系统的那台虚拟机，被称之为宿主机
* **容器**：通过 Docker 创建的轻量级虚拟机，被成为容器（Container）

以应用的角度看，容器与宿主机不是从属关系，而是并列关系  

> Docker 容器是一台真实的虚拟机，这是理解容器的关键。

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

如果只考虑 Docker 容器的操作系统属性，那么镜像=轻量级操作系统安装包。  

如果需考虑 Docker 容器的应用软件属性，那么镜像=（轻量级操作系统+应用）安装包

> 例如：MySQL 镜像= 虚拟的 Linux 操作系统 + MySQL

#### 镜像是怎么产生的？

用户编写镜像编排 Dockerfile，对这种文件进行 build 操作，就生成了一个镜像。  

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/dockerfiletoimage.png)


#### 镜像是一个文件？

镜像从逻辑上可以简单理解是一个文件，但实际上是多层文件的组合。

所以，镜像虽然不是一个单独的文件，但可以被导出成为一个压缩文件：

```
# 镜像导出成一个tarball文件
docker save image

# 加载一个 tarball 镜像文件
docker load image
```

#### 镜像存放在哪里？

运行容器时，Docker 会从 */var/lib/docker/image* 目录下寻找是否镜像文件。

如果没有镜像文件，Docker 会尝试从 [Dockerhub 镜像仓库](http://dockerhub.com/)中下载到本地，然后运行。

## 镜像仓库

众所周知，Dockerhub 是由 Docker 官方运营的全球最大的镜像仓库。  

实际上，除了 Dockerhub 之外，还有多种构建仓库的方式：

### 自建仓库

支持自建仓库。一般云提供商均提供了镜像仓库服务，供客户存放自己的私有镜像。

### 加速仓库

如果从 Dockerhub 下载镜像镜像非常慢的话，就需要通过如下的方式修改仓库地址：

1. 选择或获取你喜欢的国内镜像仓库（加速地址）
   ```
   #1 Docker 中文社区
   https://registry.docker-cn.com

   #2 网易仓库
   http://hub-mirror.c.163.com

   #3 腾讯仓库
   https://mirror.ccs.tencentyun.com

   #4 阿里云仓库
   https://f53jxx8r.mirror.aliyuncs.com
   ```
   > 上述阿里云仓库加速地址仅供参考，建议登录控制台后，从后台[获取](https://cr.console.aliyun.com/cn-zhangjiakou/instances/mirrors)获取

2. 运行如下的命令修改 Docker 默认的仓库设置
    ```
    sudo mkdir -p /etc/docker
    sudo tee /etc/docker/daemon.json <<-'EOF'
    {
      "registry-mirrors": ["https://f53jxx8r.mirror.aliyuncs.com"]
    }
    EOF
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```

## 容器

容器是 Docker 最重要的组件，上面已经多次提到容器就是一个轻量级虚拟机。  

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/container-what-is-container.png)

### 运行容器

下面我们通过一个简单的示例，介绍如何运行一个容器：

1. 找到一个 Docker 镜像，例如：[MySQL](https://hub.docker.com/_/mysql)
2. 运行如下的命令启动一个 MySQL 容器
   ```
   docker run --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:tag
   ```
3. 容器运行成功后，运行如下命令即可开始使用 MySQL 的客户端命令
   ```
   docker exec -it mysql mysql -uroot -p123456
   ```

上述示例我们完成如下几个工作：

* 通过镜像页面找到运行容器的方案
* 运行一个容器
* 进入一个容器

### 创建镜像

Docker 的原理表明，容器的内核有一部分共享的 Docker 镜像的不变文件，另外一部分是可变文件。  

所以，容器也可以很方便的转换成镜像。具体操作如下：

1. 运行 `docker ps` 命令获取容器的 ID 号

2. 将容器导出为压缩文件
    ```
    # 容器导出成 tarball 文件
    docker export -o mysql-`date +%Y%m%d`.tar f9fc8627b7fe

    # 查看文件
    ls mysql-`date +%Y%m%d`.tar
    ```

3. 将压缩文件转换成镜像
   ```
   docker import  mysql-20210416.tar mysql-test  
   ```

4. 运行 `docker image ls` 命令，查看刚转换成功的镜像
   ```
    $ docker image ls
    REPOSITORY                                                    TAG              IMAGE ID       CREATED         SIZE
    mysql-test                                                    latest           05cb947f5572   5 seconds ago   209MB
   ```

> 从功能上讲，docker export相当于commit ＋save，先将容器commit成镜像，再save成文件。

## 环境变量 

我们知道容器是用于运行应用的，既然是运行应用，自然少不了个性化的设置。  

即，一个镜像在部署的时候，是需要用户给可配置的参数赋值的。那这些参数在哪里？怎么设置？  

首先这些变量需要在构建镜像的时候提前规划并实现，用户在运行容器的时候通过 `docker run -e ` 带入参数。

## Dockerfile

Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。  

具体使用请直接阅读[官方文档](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)。

关于 Dockerfile，下面我们再传递几个重要的观点：

* Dockerfile 是 Docker 运维开发工作的关键
* Dockerfile 文件主要用于编写应用的安装过程
* 应用的初始化过程可以在 Dockerfile 中引入，然后在独立的脚本中编写
* Dockerfile 必须构建成镜像后再供用户使用，直接基于 Dockerfile 运行容器可能会由于网络问题导致无法达成预期目的

## 数据卷

Docker 容器的理念是运行时，因此它并不向普通的虚拟机一样，可以方便的更改任何文件。  

但用户在实际使用 Docker 的过程中，一定有持久保存数据（包含配置文件）的需求，那么 Docker 是如何解决这个问题的呢？

### 概述

Docker 提供了一套数据存储的方案（[卷](https://docs.docker.com/storage/volumes/)）：

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/types-of-mounts-volume.png)

主要有两种形式的存储卷模式：

|          | Named Volumes                  | Bind Mounts                   |
| -------- | ------------------------------ | ----------------------------- |
| 路径     | /var/lib/docker/volumes 目录下 | 任意位置                      |
| 启用方式 | my-volume:/usr/local/data      | /path/to/data:/usr/local/data |
| 预先定义 | 可以先定义，也可以不定义       | 不需要                        |
| 名称     | my-volume_default 或 my-volume | data                          |
| 文件权限 | 权限宽松                       | 受制于宿主机文件权限          |
| 数据方向 | 容器 →Volume                   | Volume  → 容器                |

### 使用卷

下面是一个通过 `-v` 使用卷的范例：  

```
 docker run -dp 3000:3000 \
     -w /app -v "$(pwd):/app" \
     node:12-alpine \
     sh -c "yarn install && yarn run dev"
```

### 共享卷

多个容器共享一个存储卷是非常典型的应用场景：

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/docker-sharevolume-websoft9.png)


## 网络

由于容器是用于部署应用的，因此它需要频繁的被其他服务所访问，深刻理解 Docker 网络的概念和原理就显得至关重要。

### 组网

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