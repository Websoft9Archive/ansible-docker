# Initial Installation

If you have completed the Docker deployment on Cloud Platform, the following steps is for you to start use it quikly

## Preparation

1. Get the **Internet IP** on your Cloud Platform
2. Check you **[Inbound of Security Group Rule](https://support.websoft9.com/docs/faq/tech-instance.html)** of Cloud Console to ensure the TCP:9000 is allowed if you using the Docker with Portainer

## Docker Installation Wizard

### Check Docker

1. Use the **SSH** to connect Server, and run these command below to view the installation informantion and running status
   ```
   sudo docker info
   sudo systemctl status docker
   ```
2. You can ge the message from SSH " Active: active (running)... " when Docker is running

### Login Portainer

If you use the Docker with Portainer, refer to the following steps for starting Portainer:

1. Using local Chrome or Firefox to visit the URL *http://Internet IP:9000* to visit Portainer
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-login-websoft9.png)

2. Set the username and password, then click【Create user】 

3. Select the【Local】 for default connection, then click 【Connect】
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-loginconnect-websoft9.png)

4. Go the the dashboard of Portainer, click the 【Local】 to start
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-bkselect-websoft9.png)

5. Click the 【Portainer】 
   ![](http://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-pcontainer-websoft9.png)

> More useful Docker guide, please refer to [Docker Documentation](https://docs.docker.com/)

## Q&A

#### I can't visit the start page of Portainer?

Your TCP:9000 of Security Group Rules is not allowed so there no response from Chrome or Firefox

#### How is Portainer installed?

Portainer was installed by Docker

#### How can I install Portainer if it not been included in my Docker?

You can deploy **Docker with Portainer image**, and you can run these install commands below also:

~~~
#Commands for installing Portainer

docker volume create portainer_data
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
cd /usr/libexec/docker/
sudo ln -s docker-runc-current docker-runc
~~~
