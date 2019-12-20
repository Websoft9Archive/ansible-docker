# FAQ

#### 单台服务器上是否可以跑多个容器？

只要服务器资源允许，单台服务器上可以运行成百上千个容器

#### Docker-Sender是什么？

Docker sender 是一个命令行应用程序，可用于将性能数据发送到 Docker server 进行处理。

#### Docker-Git是什么？

Docker get 是一个命令行应用，它可以用于与 Docker agent 进行通信，并从 Docker agent 那里获取所需的信息。

#### 是否可以使用云平台的 RDS 作为 Docker 的数据库？

不建议使用非服务器上的第三方数据库

#### Docker-Server 能在Windows服务器上部署吗？

官方没有提供 Windows 上的安装方案

#### Docker数据库连接配置信息在哪里？

数据库配置信息 [Docker 配置文件](/zh/stack-components.html#zabbix)中

#### 如果没有域名是否可以部署 Docker？

可以，访问`http://服务器公网IP/zabbix` 即可

#### 数据库 root 用户对应的密码是多少？

密码存放在服务器相关文件中：`/credentials/password.txt`

#### 是否有可视化的数据库管理工具？

从安全角度考虑，本部署包中没有预装 phpMyAdmin

#### 是否可以修改 Docker 的源码路径？

不建议修改

#### 如何修改上传的文件所属用户（组）和读写权限?

```shell
#例如：用户为 apache
chown -R apache.apache /data/wwwroot

#例如：用户为 nginx
chown -R nginx.nginx /data/wwwroot

#修改读写执行权限
find /data/wwwroot -type d -exec chmod 750 {} \;
find /data/wwwroot -type f -exec chmod 640 {} \;
```
#### 部署和安装有什么区别？

部署是将一序列软件按照不同顺序，先后安装并配置到服务器的过程，是一个复杂的系统工程。  
安装是将单一的软件拷贝到服务器之后，启动安装向导完成初始化配置的过程。  
安装相对于部署来说更简单一些。 

#### 云平台是什么意思？

云平台指提供云计算服务的平台厂家，例如：Azure,AWS,阿里云,华为云,腾讯云等

#### 实例，云服务器，虚拟机，ECS，EC2，CVM，VM有什么区别？

没有区别，只是不同厂家所采用的专业术语，实际上都是云服务器