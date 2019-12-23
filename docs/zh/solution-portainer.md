---
sidebarDepth: 3
---
# 可视化工具 Portainer

[Portainer](https://www.portainer.io) 是一个可视化的Docker操作界面，提供状态显示面板、应用模板快速部署、容器镜像网络数据卷的基本操作（包括上传下载镜像，创建容器等操作）、事件日志显示、容器控制台操作、Swarm集群和服务等集中管理和操作、登录用户管理和控制等功能。功能十分全面，基本能满足中小型单位对容器管理的全部需求。

## 登录 Portainer

如果你部署了包含 Portainer 的Docker环境，请直接登录使用。否则，请先安装 Portainer：

~~~
#通过命令安装 Portainer

docker volume create portainer_data
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
cd /usr/libexec/docker/
sudo ln -s docker-runc-current docker-runc
~~~

1. 通过本地浏览器访问：*http://服务器公网IP:9000*， 直接进入 Portainer 界面
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-login-websoft9.png)

2. 设置管理员账号密码，点击【Create user】

3. 选择【Local】作为镜像连接选项，然后点击【Connect】
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-loginconnect-websoft9.png)

4. 进入Portainer后台管理界面，点击Local项目就可以开始使用Portainer
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-bkselect-websoft9.png)


## 部署MySQL容器

下面详细介绍通过 Portainer 部署MySQL：

1. 登录 Portainer ，打开【Containers】>【Add container】
    ![createcontainer](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-addcontainer-websoft9.png)

2. 设置容器运行所需的参数（下面示图并描述重点设置部分）   
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-mysql-websoft9.png)

   * Name 为自定义的容器名称
   * Image 为容器镜像名称，例如"mysql:5.6" 系统会自动到[DockerHub](https://hub.docker.com/)中拉取MySQL5.6
   * Network ports configuration：建议开启【Publish all exposed network ports...】 以保证容器中的服务可以通过服务器端口被外界访问。如果不开启，需手工建立准确的映射关系(**难度系数有点高**)。
   * Env 环境变量设置：添加如所示的容器环境变量，对于MySQL镜像来说，数据库 root 密码**MySQL_ROOT_PASSWORD** 为必填变量，其他更多可选变量查看 [ MySQL镜像说明](https://hub.docker.com/_/mysql)
   * Restart policy：建议选择【Always】，使得容器无论在什么情况下停止总会自动重新启动；

3. 点击 Deploy the container 创建容器；
    
4. 如果服务器安全组的3306端口已经开放，现在就可以在本地通过远程连接 MySQL 数据库
   > MySQL8远程访问测试失败，报错：Authentication plugin caching_sha_password cannot be loaded...

## 部署WordPress容器

下面详细介绍通过 Portainer 部署WordPress以及使用上一步的MySQL作为数据存储：

1. 登录 Portainer ，打开【Containers】>【Add container】
   ![createcontainer](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-addcontainer-websoft9.png)

2. 设置容器运行所需的参数（下面示图并描述重点设置部分）   
   ![wordpress](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-wordpress-websoft9.png)

   * Name 为自定义的容器名称
   * Image 为容器镜像名称，例如"wordpress" 系统会自动到[DockerHub](https://hub.docker.com/)中拉取WordPress
   * Network ports configuration：建议开启【Publish all exposed network ports...】 以保证容器中的服务可以自动匹配服务器端口被外界访问。如果不开启，需手工建立准确的映射关系(**难度系数有点高**)。
   * Restart policy：建议选择【Always】，使得容器无论在什么情况下停止总会自动重新启动；

3. 点击 Deploy the container 创建容器，创建成功后查看映射的服务器端口号；
    
4. 本地浏览器访问：*http://服务器公网IP：端口* 即可访问 WordPress 的初始化安装界面
    ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-startinstall-1-websoft9.png)

5. 此处如果你打算使用MySQL容器，数据库主机地址填写的是 **服务器公网IP:端口**
    ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-startinstall-2-websoft9.png)

6. 数据库验证通过后，系统提示正式“进行安装” 
  ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/wordpress/wordpress-install003-websoft9.png)


## 进阶实战：Nginx 容器实现端口转发

以上一章节中的 Wordpress 网站作为示例，在 WordPress 部署完成后，需要在浏览器内输入 *http://公网IP地址：端口* 的形式访问网站，但我们不加端口就能访问域名，所以这时就要用到 Nginx 的端口转发功能。

要实现这个需求，有三个部署：部署Nginx容器，部署 FileBrowser 容器，

### 部署Nginx容器

下面详细介绍通过 Portainer 部署 Nginx：

1. 登录 Portainer ，打开【Containers】>【Add container】
   ![createcontainer](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-addcontainer-websoft9.png)

2. 按照下图创建 Nginx 容器；
   ![nginx-1](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-nginx-1-websoft9.png)

3. 回到容器列表，点击刚刚创建的 Nginx 容器，可进入到容器详情页，往下可看到 **volueme** 信息，记录下 **/etc/nginx 目录** 对应的 volume 的值。
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-nginx-volume-websoft9.png)


### 部署 FileBrowser 容器

1. 进入到 Portainer 页面，选择左边的 **App Templates** 选项，往下找到 **File browser** 容器模板，单击选择；
    ![filebowser-1](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-filebrowser-1-websoft9.png)
2. 按照下图创建 File browser 容器；
    ![filebowser-2](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-filebrowser-2-websoft9.png)
3. 进入到容器列表，单击刚刚创建的 File browser 容器，点击 **Duplicate/Edit** 按钮，进入到修改容器信息页面；
    ![filebowser-3](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-filebrowser-3-websoft9.png)
4. 按照下图，将 File browser 的 volume 值修改为 和 Nginx 的 volume 值相同；
    ![filebowser-4](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-filebrowser-4-websoft9.png)


### 设置 Nginx 配置文件实现端口转发

1. 打开第二步中创建的 File Browser 网站（[公网ip:端口]()），账号密码为 **admin/admin**，登录到 File Browser;
    ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-filebrowser-5-websoft9.png)
2. 进入 **conf.d** 目录，双击 **default.conf** 文件，将原来的配置删除，修改为如下图所示内容：
    ~~~
    upstream wordpress {
        server 159.138.6.145:32773;
    }

    server {
        listen 80;
        server_name  test.example.top; #绑定域名
    
        location / {
        proxy_pass http://wordpress;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Via "nginx";
        }
    }
    ~~~
    ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-nginx-2-websoft9.png)
    >[danger] 注意：请将 **server_name** 改为自己的域名，**proxy_pass** 改为自己网站容器的 **IP:端口号**
3. 到容器列表重启 Nginx 容器，现在就可以在浏览器地址栏输入域名直接访问自己的网站了。
    ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-proxysuccess-websoft9.png)

> 修改完 Nginx 的配置文件后，File Browser 容器可选择继续保留使用或删除。


## 进阶实战：Portainer 设置 HTTPS

1. 参考 [安装File Browser容器](portainer/solution/filebrowser.md) 章节新建 File Browser 容器；
    > 注意设置 File Browser 的 volume 。

2. 在浏览器打开 File Browser ，新建一个名为 **cert** 文件夹，将证书上传至 cert；
    ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-ssl-1-websoft9.png)

3.修改 Nginx 的配置文件,注意将 IP 和域名改成自己的服务器 IP 和域名；    

    upstream portainer {
    server 159.138.6.145:9000;
    }

    server {
        listen 80;
        listen 443 ssl;
        server_name  test.websoft9.top;

        ssl_certificate /etc/nginx/cert/cert-1540972394298_test.websoft9.top.crt;
        ssl_certificate_key /etc/nginx/cert/cert-1540972394298_test.websoft9.top.key;
 
        location / {
        proxy_pass http://portainer;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Via "nginx";
        }
    }
   

 ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-ssl-2-websoft9.png)
   >[warning] 如果证书路径和图中不同，请将图中证书路径改为自己的证书所在路径，并将证书名改为自己的证书名。
4. 在容器列表中重启 Nginx 容器使配置生效，就可以在浏览器内使用 https://域名 访问 Portainer 了。
5. 如果想要达到访问 http 自动跳转到 https 的效果，请将配置改成如下所示：

        upstream portainer {
        server 159.138.6.145:9000;
        }

        server {
            listen 80;
            listen 443 ssl;
            server_name  test.websoft9.top;
    
        ssl_certificate /etc/nginx/cert/cert-1540972394298_test.websoft9.top.crt;
        ssl_certificate_key /etc/nginx/cert/cert-1540972394298_test.websoft9.top.key;
        
        if ($scheme != "https") {
    	return 301 https://$host$request_uri;
    	}
    
        location / {
        proxy_pass http://portainer;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Via "nginx";
        }
        }

## 技巧

### Portainer 运行容器内部命令

在此以连接到 MySQL 容器为例进行说明：

1. 返回到容器列表，点击下图中 MySQL 的 **Quick actions** 一栏下的 **>_** 图标；
    ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-containerlist-websoft9.png)
2. 在新打开的页面，点击 **Connetc** 按钮，连接到容器；
    ![](http://libs-websoft9-com.oss-cn-qingdao.aliyuncs.com/Websoft9/DocsPicture/zh/potainer/portainer-createdatabase-websoft9.png)
3. 接下来就可以在命令窗口中输入```mysql -uroot -ppassword;"```,其中 “password” 为您在自己设置的数据库密码，这样就可以开始使用数据库命令对 MySQL 进行管理了；

### Portainer 备份

到 Portainer 的容器列表里面查看 portainer 的 volume 对应的服务器目录，在```/var/lib/docker/volumes```下可找到 volume 对应的目录名，将其备份即可。

### Portainer 升级

只需运行 ```docker pull portainer```就可以将 Portainer 升级到最新版本。

### Portaniner 绑定域名

域名绑定可在 [配置Nginx实现端口转发](/zh/solution-portainer.md#设置-nginx-配置文件实现端口转发) 章节中将 server_name 改成自己的域名即可。

## 常见问题

#### 不知道容器镜像所需的端口怎么办？

建议开启【Publish all exposed network ports...】 以保证容器中的服务可以自动匹配服务器端口被外界访问。如果不开启，需自行到[DockerHub](https://hub.docker.com/)网站查看端口。

#### 容器的端口与服务器的端口有什么区别？

容器端口需要通过服务器端口做映射，才可以被互联网用户访问。

