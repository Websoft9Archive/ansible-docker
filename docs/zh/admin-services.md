# 服务启停

使用由 Websoft9 提供的 Docker 部署方案，可能需要用到的服务如下：

### Docker系统服务

```shell
sudo systemctl start docker
sudo systemctl restart docker
sudo systemctl stop docker
sudo systemctl status docker
```

### Docker-compose服务

```
#创建容器编排
sudo docker-compose up

#创建容器编排并重建有变化的容器
sudo docker-compose up -d

#启动/重启
sudo docker-compose start
sudo docker-compose stop
sudo docker-compose restart
```

### 运行时容器管理

> 终止命令 `stop` 会从进程中释放容器的资源，但不会删除容器

```shell
#示例：mysql
sudo docker pause mysql
sudo docker stop mysql

#示例：redis
sudo docker pause redis
sudo docker stop redis
```
