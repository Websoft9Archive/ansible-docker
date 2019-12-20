# 初始化安装

在云服务器上部署 Docker 预装包之后，请参考下面的步骤快速入门。

## 准备

1. 在云控制台获取您的 **服务器公网IP地址** 
2. 如果使用 Portainer，请在云控制台安全组中，检查 **Inbound（入）规则** 下的 **TCP:9000** 端口是否开启

## Docker 初始化向导

### 检测 Docker 安装

1. 使用 SSH 连接服务器，运行下面的命令，查看 Docker 的安装信息和运行状态
   ```
   sudo docker info
   sudo systemctl status docker
   ```
2. 运行服务状态查询命令，Docker 正常运行会得到 " Active: active (running)... " 的反馈

### 登录 Portainer

如果部署了 Portainer，请参考如下的初始化步骤：

1. 通过本地浏览器访问：*http://服务器公网IP:9000*， 直接进入 Portainer 界面
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-login-websoft9.png)

2. 设置管理员账号密码，点击【Create user】

3. 选择【Local】作为镜像连接选项，然后点击【Connect】
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-loginconnect-websoft9.png)

4. 进入Portainer后台管理界面，点击Local项目就可以开始使用Portainer
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-bkselect-websoft9.png)

5. 通过 Portainer 查看运行容器，你会发现 Portainer 本身也是运行在容器中的
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-pcontainer-websoft9.png)

> 需要了解更多 Docker 的使用，请参考官方文档：[Docker Documentation](https://docs.docker.com/)

## 常见问题

#### 浏览器无法访问 Portainer（白屏没有结果）？

您的服务器对应的安全组9000端口没有开启（入规则），导致浏览器无法它

#### Portainer是如何安装的？

Portainer 自身也是采用Docker安装

#### 如果我用的部署包中没有Portainer，如何安装它？

可以将服务器当前镜像更换为Portainer镜像，也可以在当前镜像的基础通过命令安装

~~~
#通过命令安装 Portainer

docker volume create portainer_data
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
cd /usr/libexec/docker/
sudo ln -s docker-runc-current docker-runc
~~~