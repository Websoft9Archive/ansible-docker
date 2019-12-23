---
sidebarDepth: 3
---
# Docker GUI: Portainer

[Portainer](https://www.portainer.io) was developed to help customers adopt Docker container technology and accelerate time-to-value. It has never been so easy to build, manage and maintain your Docker environments. Portainer is easy to use software that provides an intuitive interface for both software developers and IT operations. Portainer gives you a detailed overview of your Docker environments and allows you to manage your containers, images, networks and volumes. Portainer is simple to deploy - you are just one Docker command away from running Portainer anywhere.

## Login Portainer

If you have deployed the Portainer, please login it directly. Otherwise, please install it first referring to the commands below:

~~~
#Commands for installing Portainer

docker volume create portainer_data
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
cd /usr/libexec/docker/
sudo ln -s docker-runc-current docker-runc
~~~

1. Use local Chrome or Firefox to visit: *http://Internet IP:9000* , enter to Portainer GUI
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-login-websoft9.png)

2. Set the administrator username and password, then click【Create user】

3. Select teh 【Local】for connection, then click the 【Connect】
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-loginconnect-websoft9.png)

4. Now, you can see the Portainer console and click 【Local】to configure Portainer
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-bkselect-websoft9.png)


## Sample: Start MySQL Container

The following details the deployment of MySQL through Portainer:

1. Login Portainer, open【Containers】>【Add container】
    ![createcontainer](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-addcontainer-websoft9.png)

2. Set the parameters required for container operation (shown below and describe key settings)
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-mysql-websoft9.png)

   * Name: define your container name
   * Image: image name in [DockerHub](https://hub.docker.com/)
   * Network ports configuration：suggest your enable【Publish all exposed network ports...】
   * Env: variable of image, e.g. the root password**MySQL_ROOT_PASSWORD** is required variable. More variable please read the [ MySQL image docs](https://hub.docker.com/_/mysql)
   * Restart policy：suggest your select 【Always】

3. Click【Deploy the container】 to start Container
    
4. If you want to connect MySQL remotely, please enable your Server's Port 3306 of Security Group

## Sample: Start WordPress Container

The following details the deployment of WordPress through Portainer and WordPress installation:

1. Login Portainer, open【Containers】>【Add container】
   ![createcontainer](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-addcontainer-websoft9.png)

2. Set the parameters required for container operation (shown below and describe key settings) 
   ![wordpress](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-wordpress-websoft9.png)

   * Name: define your container name
   * Image: image name in [DockerHub](https://hub.docker.com/)
   * Network ports configuration：suggest your enable【Publish all exposed network ports...】
   * Env: suggest you set the MySQL variables for container at this step
   * Restart policy：suggest your select 【Always】

3. Click【Deploy the container】 to start Container
    
4. Use you local Firefox or Chrome to visit: *http://Internet IP:port* you can see the WordPress installation interface
    ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/potainer/portainer-startinstall-1-websoft9.png)

5. If you want to use the MySQL container for WordPress, Database host format is **Internet IP:port**  
    ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/wp04.png)

### Portainer backup

Backup the volumes (```/var/lib/docker/volumes```) means backup all data.

### Portainer upgrade

Just run the command ```docker pull portainer``, you can update Portainer very easily

## FAQ

#### How can I set the Port mapping?

Suggest you enable 【Publish all exposed network ports...】 to make sure the correct Port mapping automatic, otherwise you should configure it manually by referring to  [DockerHub](https://hub.docker.com/) documentation.

#### What the difference between Docker's Port and Server's Port?

Docker's Port must beed set Port mapping with Server's Port, then user can visit it from Internet