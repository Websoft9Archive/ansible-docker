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

## 原理

Docker 的核心原理可以归纳为两点：**虚拟文件系统+虚拟用户**。

什么意思呢？ 

* 虚拟文件系统本质上还是宿主机上的文件，但通过虚拟技术就变成了一种“独占”资源。  
* 虚拟用户本质上还是宿主机上的用户，但通过虚拟技术让容器认为自己有了单独的用户管理。  

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

### 原理

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

### 仓库

众所周知，DockerHub 是由 Docker 官方运营的全球最大的镜像仓库。  

实际上，除了 DockerHub 之外，还有多种构建仓库的方式：

#### 自建仓库

支持自建仓库。一般云提供商均提供了镜像仓库服务，供客户存放自己的私有镜像。

#### 加速仓库

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

2. 修改 */etc/docker/daemon.json* 文件（如果没有可以增加），插入下值
    ```
    {
      "registry-mirrors": ["https://f53jxx8r.mirror.aliyuncs.com"]
    }
    ```

3. 重启服务后生效
    ```
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```

4. Docker 支持配置多个仓库地址，类似：
    ```
    {
      "registry-mirrors": ["https://registry.docker-cn.com","https://f53jxx8r.mirror.aliyuncs.com","https://docker.mirrors.ustc.edu.cn","http://hub-mirror.c.163.com"]
    }
    ```


## 容器

容器是 Docker 最重要的组件，上面已经多次提到容器就是一个轻量级虚拟机。  

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/container-what-is-container.png)

### 运行容器

通过 docker run 命令运行容器，它的用法和参数如下（[详情](https://github.com/docker/cli/blob/master/docs/reference/run.md)）。

```
Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container

Options:
      --add-host list                  Add a custom host-to-IP mapping (host:ip)
  -a, --attach list                    Attach to STDIN, STDOUT or STDERR
      --blkio-weight uint16            Block IO (relative weight), between 10 and 1000, or 0 to disable (default 0)
      --blkio-weight-device list       Block IO weight (relative device weight) (default [])
      --cap-add list                   Add Linux capabilities
      --cap-drop list                  Drop Linux capabilities
      --cgroup-parent string           Optional parent cgroup for the container
      --cgroupns string                Cgroup namespace to use (host|private)
                                       'host':    Run the container in the Docker host's cgroup namespace
                                       'private': Run the container in its own private cgroup namespace
                                       '':        Use the cgroup namespace as configured by the
                                                  default-cgroupns-mode option on the daemon (default)
      --cidfile string                 Write the container ID to the file
      --cpu-period int                 Limit CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int                  Limit CPU CFS (Completely Fair Scheduler) quota
      --cpu-rt-period int              Limit CPU real-time period in microseconds
      --cpu-rt-runtime int             Limit CPU real-time runtime in microseconds
  -c, --cpu-shares int                 CPU shares (relative weight)
      --cpus decimal                   Number of CPUs
      --cpuset-cpus string             CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string             MEMs in which to allow execution (0-3, 0,1)
  -d, --detach                         Run container in background and print container ID
      --detach-keys string             Override the key sequence for detaching a container
      --device list                    Add a host device to the container
      --device-cgroup-rule list        Add a rule to the cgroup allowed devices list
      --device-read-bps list           Limit read rate (bytes per second) from a device (default [])
      --device-read-iops list          Limit read rate (IO per second) from a device (default [])
      --device-write-bps list          Limit write rate (bytes per second) to a device (default [])
      --device-write-iops list         Limit write rate (IO per second) to a device (default [])
      --disable-content-trust          Skip image verification (default true)
      --dns list                       Set custom DNS servers
      --dns-option list                Set DNS options
      --dns-search list                Set custom DNS search domains
      --domainname string              Container NIS domain name
      --entrypoint string              Overwrite the default ENTRYPOINT of the image
  -e, --env list                       Set environment variables
      --env-file list                  Read in a file of environment variables
      --expose list                    Expose a port or a range of ports
      --gpus gpu-request               GPU devices to add to the container ('all' to pass all GPUs)
      --group-add list                 Add additional groups to join
      --health-cmd string              Command to run to check health
      --health-interval duration       Time between running the check (ms|s|m|h) (default 0s)
      --health-retries int             Consecutive failures needed to report unhealthy
      --health-start-period duration   Start period for the container to initialize before starting health-retries countdown (ms|s|m|h) (default 0s)
      --health-timeout duration        Maximum time to allow one check to run (ms|s|m|h) (default 0s)
      --help                           Print usage
  -h, --hostname string                Container host name
      --init                           Run an init inside the container that forwards signals and reaps processes
  -i, --interactive                    Keep STDIN open even if not attached
      --ip string                      IPv4 address (e.g., 172.30.100.104)
      --ip6 string                     IPv6 address (e.g., 2001:db8::33)
      --ipc string                     IPC mode to use
      --isolation string               Container isolation technology
      --kernel-memory bytes            Kernel memory limit
  -l, --label list                     Set meta data on a container
      --label-file list                Read in a line delimited file of labels
      --link list                      Add link to another container
      --link-local-ip list             Container IPv4/IPv6 link-local addresses
      --log-driver string              Logging driver for the container
      --log-opt list                   Log driver options
      --mac-address string             Container MAC address (e.g., 92:d0:c6:0a:29:33)
  -m, --memory bytes                   Memory limit
      --memory-reservation bytes       Memory soft limit
      --memory-swap bytes              Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --memory-swappiness int          Tune container memory swappiness (0 to 100) (default -1)
      --mount mount                    Attach a filesystem mount to the container
      --name string                    Assign a name to the container
      --network network                Connect a container to a network
      --network-alias list             Add network-scoped alias for the container
      --no-healthcheck                 Disable any container-specified HEALTHCHECK
      --oom-kill-disable               Disable OOM Killer
      --oom-score-adj int              Tune host's OOM preferences (-1000 to 1000)
      --pid string                     PID namespace to use
      --pids-limit int                 Tune container pids limit (set -1 for unlimited)
      --platform string                Set platform if server is multi-platform capable
      --privileged                     Give extended privileges to this container
  -p, --publish list                   Publish a container's port(s) to the host
  -P, --publish-all                    Publish all exposed ports to random ports
      --pull string                    Pull image before running ("always"|"missing"|"never") (default "missing")
      --read-only                      Mount the container's root filesystem as read only
      --restart string                 Restart policy to apply when a container exits (default "no")
      --rm                             Automatically remove the container when it exits
      --runtime string                 Runtime to use for this container
      --security-opt list              Security Options
      --shm-size bytes                 Size of /dev/shm
      --sig-proxy                      Proxy received signals to the process (default true)
      --stop-signal string             Signal to stop a container (default "SIGTERM")
      --stop-timeout int               Timeout (in seconds) to stop a container
      --storage-opt list               Storage driver options for the container
      --sysctl map                     Sysctl options (default map[])
      --tmpfs list                     Mount a tmpfs directory
  -t, --tty                            Allocate a pseudo-TTY
      --ulimit ulimit                  Ulimit options (default [])
  -u, --user string                    Username or UID (format: <name|uid>[:<group|gid>])
      --userns string                  User namespace to use
      --uts string                     UTS namespace to use
  -v, --volume list                    Bind mount a volume
      --volume-driver string           Optional volume driver for the container
      --volumes-from list              Mount volumes from the specified container(s)
  -w, --workdir string                 Working directory inside the container
```

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

Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的[指令](https://github.com/docker/cli/blob/master/docs/reference/builder.md)和说明。  

> 理解每个指令的用法是掌握 Docker 技术的关键

具体使用请直接阅读[官方文档](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)。

关于 Dockerfile，下面我们再传递几个重要的观点：

* Dockerfile 是 Docker 运维开发工作的关键
* Dockerfile 文件主要用于编写应用的安装过程
* 应用的初始化过程可以在 Dockerfile 中引入，然后在独立的脚本中编写
* Dockerfile 必须构建成镜像后再供用户使用，直接基于 Dockerfile 运行容器可能会由于网络问题导致无法达成预期目的

指令不仅仅用于设计 Docker 镜像，还有一部分指令与容器运行时密切相关，包括：

* CMD
* ENTRYPOINT
* WORKDIR
* ENV
* USER
* VOLUME


### CMD 和 ENTRYPOINT

它们都是容器启动时运行的指令。

有如下几个关键技术点需要掌握：  

1. CMD 与 ENTRYPOINT 的区别：CMD 直接运行单条命令，ENTRYPOINT 用于运行一个脚本
2. 指令的 Shell 和 Exec 语法模式

  ```
  # Shell 模式
  CMD ping localhost

  # Exec 模式
  CMD ["/bin/ping","localhost"] 
  ```

  可见它们从写法上一种是命令行模式，一种是数组模式。  

  但它们不仅仅写法上不同，更重要的是运行方式不同。  
  
  * CMD 模式相当于调用 Shell 后再运行指令，例如上面的例子实际上相当于： /bin/sh -c "ping localhost"
  * ENTRYPOINT 模式相当于直接运行指令，例如上面的例子实际上相当于： /bin/ping localhost

3. CMD 与 ENTRYPOINT 组合使用：组合使用的时候 CMD 作为 ENTRYPOINT 的一个参数

组合使用 ENTRYPOINT 和 CMD 命令式, 确保你一定用的是 Exec 表示法. 如果用其中一个用的是Shell表示法, 或者一个是Shell表示法, 另一个是Exec表示法, 你永远得不到你预期的效果.

下表列出了如果把Shell表示法和Exec表示法混合, 最终得到的命令行, 可以看到如果有Shell表示法存在, 很难得到正确的效果:

  ```
  Dockerfile    Command

  ENTRYPOINT /bin/ping -c 3
  CMD localhost               /bin/sh -c '/bin/ping -c 3' /bin/sh -c localhost


  ENTRYPOINT ["/bin/ping","-c","3"]
  CMD localhost               /bin/ping -c 3 /bin/sh -c localhost

  ENTRYPOINT /bin/ping -c 3
  CMD ["localhost"]"          /bin/sh -c '/bin/ping -c 3' localhost

  ENTRYPOINT ["/bin/ping","-c","3"]
  CMD ["localhost"]            /bin/ping -c 3 localhost
  ```

从上面看出, 只有ENTRYPOINT 和 CMD 都用 Exec 表示法, 才能得到预期的效果。


### WORKDIR

### ENV

### USER

先看官方的定义：  

The USER instruction sets the user name (or UID) and optionally the user group (or GID) to use when running the image and for any RUN, CMD and ENTRYPOINT instructions that follow it in the Dockerfile.

有几个关键信息：  

* 适用于 RUN, CMD and ENTRYPOINT 三个指令

### VOLUME

对于 VOLUME 申明的目录，容器运行后会自动在 /var/lib/docker/volumes 下创建如下的匿名卷。  

```
/var/lib/docker/volumes/b58ec6901901202b215315db8d958848d910d51dc37c781e29c133064ed5842d
```

当我们运行 `docker-compose down` 删除容器时，匿名卷不会被删除，只有运行 `docker-compose down -v` 才会删除这个卷。

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
| 空目录下数据方向 | 容器 → Named Volume                   | Bind Volume  → 容器                |
| 非空目录下数据方向 | Named Volume  → 容器                   | Bind Volume  → 容器                |


我们根据持久化数据挂载的几种场景进行试验，得出如下的现象：  

* 容器启动后由 CMD 和 ENTRYPOINT 产生的数据区别于镜像中 COPY/ADD 层的数据，前者我们称之动态数据，后者为静态数据。显然，动态数据不受挂载影响。
* 宿主机目录优先定律：挂载双方都有数据时，宿主机目录覆盖容器目录
* Bind Mounts 与 Named Volumes 有差异：Bind Mounts 任何情况下都会覆盖容器目录，而 Named Volumes 挂载空目录时会先拷贝容器目录的数据


Named Volumes 在 Docker 中被推荐为首选方式，它与 Bind Mounts 相比，有以下优点：

* 与 Bind Mounts 相比，Named Volumes 更容易备份或迁移。
* 可以使用 Docker CLI 命令或 Docker API 来管理。
* Named Volumes 在 Linux 和 Windows 容器上都能工作。
* Named Volumes 可以在多个容器之间更安全的共享。
* Named Volumes 驱动程序允许你在远程主机或云上提供存储、加密或其他功能。
* 新 Named Volumes 的内容可以由容器预填充。


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

## 用户

一般来说 Docker 不建议以 root 用户运行容器进程，因此 Dockerfile 的编写者都会在代码中创建普通用户，然后以普通用户运行进程。  

如果没有创建普通用户，容器就会默认以 root 用户权限运行

> 容器的 root 与宿主机的 root 是同一个用户，但容器 root 的权限是有限的，如果加上 --privileged=true，那么它就等同于宿主机 root 权限

![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/docker-uidgid-websoft9.png)

### UID

虽然有用户名的概念，但由于 Linux 内核最终管理的用户对象是以 uid 为标识，所以本节均以 uid 来替代用户名。  

容器由于是基于虚拟隔离技术的并共享操作系统内核的独立进程，而内核只管理一套 uid 和 gid，所以容器中的 uid 和 gid 实际上与宿主机内核是一套体系。

> 理解容器中用户权限、uid、gid 等本质，重点在于理解 《Linux User Namespace》

当容器进程尝试写文件时，内核会检查此容器的 uid 和 gid，以确定其是否具有足够的特权来修改文件。

### 提权

我们在 Dockerfile 会发现，当需要对用户提权的时候，采用的不是 su，而是下面两个命令的组合

* gosu
* exec

## 进程

有人说，容器的本质就是进程。不管这句话是否绝对，但可见进程对于容器的重要性不言而喻。  


### 查询进程

通过运行 `docker top containerid` 查询进程。  

为了便于理解，我们先运行一个Docker应用：[docker-wordpress](https://github.com/websoft9/docker-wordpress)  

然以后分别查询各个容器的进程。  

```

[root@test ~]# docker top wordpress-mysql
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
polkitd             22107               22080               0                   Aug01               ?                   00:01:52            mysqld

[root@test ~]# docker top wordpress
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
33                  807                 22090               0                   Aug01               ?                   00:00:00            apache2 -DFOREGROUND
33                  1675                22090               0                   Aug01               ?                   00:00:01            apache2 -DFOREGROUND
33                  2935                22090               0                   Aug01               ?                   00:00:00            apache2 -DFOREGROUND
33                  21955               22090               0                   Aug01               ?                   00:00:00            apache2 -DFOREGROUND
root                22090               22054               0                   Aug01               ?                   00:00:06            apache2 -DFOREGROUND
33                  26327               22090               0                   Aug01               ?                   00:00:00            apache2 -DFOREGROUND
33                  28793               22090               0                   Aug01               ?                   00:00:01            apache2 -DFOREGROUND
33                  30253               22090               0                   Aug01               ?                   00:00:00            apache2 -DFOREGROUND
33                  31445               22090               0                   Aug01               ?                   00:00:01            apache2 -DFOREGROUND
33                  31955               22090               0                   Aug01               ?                   00:00:01            apache2 -DFOREGROUND
33                  32734               22090               0                   Aug01               ?                   00:00:01            apache2 -DFOREGROUND
```

可见有的容器只运行了一个进程，而有的容器运行了多个进程（Apache 作为HTTP服务器，其天生是多进程设计）。  

也可以进入其中一个容器，再运行 `ps -ef` 命令查看进程：

```
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 Aug01 ?        00:00:06 apache2 -DFOREGROUND
www-data   153     1  0 Aug01 ?        00:00:01 apache2 -DFOREGROUND
www-data   181     1  0 Aug01 ?        00:00:00 apache2 -DFOREGROUND
www-data   193     1  0 Aug01 ?        00:00:01 apache2 -DFOREGROUND
www-data   209     1  0 Aug01 ?        00:00:01 apache2 -DFOREGROUND
www-data   214     1  0 Aug01 ?        00:00:01 apache2 -DFOREGROUND
www-data   215     1  0 Aug01 ?        00:00:00 apache2 -DFOREGROUND
www-data   218     1  0 Aug01 ?        00:00:01 apache2 -DFOREGROUND
www-data   219     1  0 Aug01 ?        00:00:00 apache2 -DFOREGROUND
www-data   224     1  0 Aug01 ?        00:00:00 apache2 -DFOREGROUND
www-data   225     1  0 Aug01 ?        00:00:00 apache2 -DFOREGROUND
root       253     0  0 06:17 pts/0    00:00:00 bash
root       261   253  0 06:18 pts/0    00:00:00 ps -ef
```

可见，两者的效果是一样的。  

新开一个 Shell 窗口，再运行 `pstree -a` 命令，回看到如下的进程树  

```
  ├─containerd-shim -namespace moby -id 8a7712fe435afaa79c08e7281de7e1a658cd00261fecc7ba02da1847d47d1715 -address /run/containerd/containerd.sock
  │   ├─apache2 -DFOREGROUND
  │   │   ├─apache2 -DFOREGROUND
  │   │   ├─apache2 -DFOREGROUND
  │   │   ├─apache2 -DFOREGROUND
  │   │   ├─apache2 -DFOREGROUND
  │   │   ├─apache2 -DFOREGROUND
  │   │   ├─apache2 -DFOREGROUND
  │   │   ├─apache2 -DFOREGROUND
  │   │   ├─apache2 -DFOREGROUND
  │   │   ├─apache2 -DFOREGROUND
  │   │   └─apache2 -DFOREGROUND
  │   ├─bash
  │   └─12*[{containerd-shim}]
  ├─containerd-shim -namespace moby -id d287c79eaced1fcdde94b2b6d45781937cb17a0ddf4848d26907dee40602e80f -address /run/containerd/containerd.sock
  │   ├─mysqld
  │   │   └─30*[{mysqld}]
  │   └─13*[{containerd-shim}]

```

你会发现，这个查询结果也基本类同。  


### 创建进程

通过上面的说明，我们已经有了非常具体的进程印象，那么现在我们再深入一些：容器的进程是如何创建的呢？

我们先回顾 Dockerfile 中的 CMD 和 ENTRYPOINT，其他它就是容器的运行时，镜像提供了容器运行所需的软件包和软件环境，但如果不通过 CMD 和 ENTRYPOINT 来启动各种应用，容器就不会产生进程。

### 非服务进程

容器一般的用于承载服务，但在开发中，容器镜像也可以用作短暂的进程：在我们计算机上运行的、容器化的可执行命令。这些容器执行单一的任务，生命周期短暂，而且通常可以在使用后被删除。我们称之为可执行镜像，这样的镜像创建的容器的进程可以称之为**非服务进程**。  

### 主进程

在Docker中有一个很特殊的进程（PID=1 的进程），这也是Docker的主进程，通过 Dockerfile 中的 ENTRYPOINT 和/或 CMD指令指定。当主进程退出的时候，容器所拥有的 PIG 命名空间就会被销毁，容器的生命周期也会结束 Docker 最佳实践建议的是一个 container 一个 service，并不强制要你一个container一个线程。有的服务，会催生更多的子进程，比如 Apache 和 uwsgi，这是完全可以的。  

PID1进程需要对自己创建的子进程负责，当主进程没有设计好，不能优雅地让子进程退出，就会照成很多问题，比如数据库 container，如果处理数据的进程没有优雅地退出，可能会照成数据丢失。如果很不幸，你的主进程就是这种管理不了子进程的那种，Docker 提供了一个小工具，帮助你来完成这部分内容。你只需要在 run 创建 container 的时候提供一个 —init flag 就行，Docker 就会手动为你处理好这些问题。  

Docker 的主进程由于是一个很特殊的存在，它的生命周期就是 docker container 的生命周期，它得对产生的子进程负责，在写 Dockerfile 的时候，务必明确 PID1 进程是什么。  

## 编排

编排就是将多个运行的容器串联起来，最常见的编排工具是 Docker Compose。  

### 范例

下面是 OnlyOffice 安装所用的 docker-compose 配置文件：

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

### 指令用法

下面重点描述重要指令的用法：

#### command

command 指令会覆盖 Dockerfile 中定义的 CMD 变量对应的值。  

例如： command: bundle exec thin -p 3000

除了支持上述单一指令之外，也支持多指令的编写。  

**多指令**

```
version: '3.1'
services:
  db:
    image: postgres
  web:
    build: .
    command:
      - /bin/bash
      - -c
      - |
        python manage.py migrate
        python manage.py runserver 0.0.0.0:8000

    volumes:
      - .:/code
    ports:
      - "8000:8000"
    links:
      - db
```

以上编排中的 command 相当于 docker run 下的：`command: bash -c "python manage.py migrate && python manage.py runserver 0.0.0.0:8000"`   

如果多指令涉及变量，也可以写成：  

```
    command:
      - /bin/bash
      - -c
      - |
        var=$$(echo 'foo')
        echo $$var # prints foo
```

#### entrypoint

entrypoint 用法与 command 类似，也支持多命令。  

## 集群

容器技术不仅仅可以运行在单台宿主机上，它也支持运行在多个主机集群上。有两种类型的流行集群工具：

* Docker Swarm：Docker 官方出品的容器的集群和调度工具。借助 Swarm，IT 管理员和开发人员可以将 Docker 节点集群建立和管理为单个虚拟系统。
* Kubernetes（k8s）：跨主机集群的自动部署、扩展以及运行应用程序容器的开源平台，这些操作包括部署，调度和节点集群间扩展。


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
