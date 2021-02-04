# 更新升级

网站技术日新月异，**更新升级**是维护工作之一，长时间不升级的程序，就如长时间不维护的建筑物一样，会加速老化、功能逐渐缺失直至无法使用。  

这里注意更新与升级这两词的差异（[延伸阅读](https://support.websoft9.com/docs/faq/zh/tech-upgrade.html#更新-vs-升级)），例如：  

- 操作系统打个补丁常称之为**更新**，Ubuntu16.04 变更为 Ubuntu18.04，称之为**升级**
- MySQL5.6.25-->MySQL5.6.30 常称之为**更新**，MySQL5.6->MySQL5.7 称之为**升级**

Docker 完整的更新升级包括：系统级更新（操作系统和运行环境）和 Docker 程序升级两种类型

## 系统级更新

运行一条更新命令，即可完成系统级更新：

``` shell
#For Ubuntu&Debian
apt update && apt upgrade -y

#For Centos&Redhat
yum update -y
```
> 本部署包已预配置一个用于自动更新的计划任务。如果希望去掉自动更新，请删除对应的Cron

## Docker 更新升级

上面的命令会同时升级 Docker 程序本体

## 容器升级

### Docker 命令升级

以正在运行的 MySQL 容器为例，如果没有持久化卷，容器的升级步骤：

```
#更新镜像
docker pull mysql

#停止容器
docker stop my-mysql-container

#删除容器
docker rm my-mysql-container

#重载容器
docker run --name=my-mysql-container --restart=always \
  -e MYSQL_ROOT_PASSWORD=mypwd -v /my/data/dir:/var/lib/mysql -d mysql
```

### Docker-Compose 命令升级

如果使用的是 Docker-Compose 启动的容器，升级容器只需运行如下三条命令：

```
docker-compose down -v
docker-compose pull
docker-compose up -d
```