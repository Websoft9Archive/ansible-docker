# 故障处理

此处收集使用 Docker 过程中最常见的故障，供您参考

> 大部分故障与云平台密切相关，如果你可以确认故障的原因是云平台造成的，请参考[云平台文档](https://support.websoft9.com/docs/faq/zh/tech-instance.html)

#### Docker service 无法启动？

先通过 `systemctl status docker` 和 `journalctl -xe` 查看错误日志。

* 如果错误日志是  Unit docker.socket entered failed state，表明系统缺少 docker 用户组，运行 `groupadd docker` 增加用户组

#### 容器无法启动？

最常见的原因是用户没有按照该容器的要求，设置必须的环境变量，导致容器启动失败

```shell
#查看容器日志，name是容器名称
sudo docker logs name
```

#### 容器端口映射失败？

如果用户无法把握宿主机（服务器）操作系统端口与容器端口的映射关系、可用性情况，请开启端口自动映射

#### MySQL 容器无法远程访问？

导致这个问题的可能原因有三点：

1. 端口映射设置错误，导致容器没有网络
2. 容器没有开启远程访问权限
3. MySQL 8.0 特殊设置要求

