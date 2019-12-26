# 服务启停

使用由Websoft9提供的 Docker 部署方案，可能需要用到的服务如下：

### Docker

```shell
sudo systemctl start docker
sudo systemctl restart docker
sudo systemctl stop docker
sudo systemctl status docker
```

### 容器

### 容器暂停/终止

> 终止命令 `stop` 会从进程中释放容器的资源，但不会删除容器

```shell
#示例：mysql
sudo docker pause mysql
sudo docker stop mysql

#示例：redis
sudo docker pause redis
sudo docker stop redis
```
