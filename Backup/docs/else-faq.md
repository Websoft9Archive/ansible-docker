# FAQ

#### Can I run many containers in one Server?

Yes, you can run more than hundreds of containers if the Server have enough computing resources

#### What is the C/S model of Docker?

After the installation of Docker, Host Machine(Sever) will run  Docker Daemon process, and one Docker client communicate with this process. But this client is detached from the Docker Daemon process which we call it Docker Server. You can use any Docker client to communicate with the Docker Server from different machine.
![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/docker-cs-websoft9.png)

#### Can I use the SSH to connect and running command from the Container?

Yes, you can use command if you have enable the SSH connection of Container
![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/docker/portainer/portainer-console-websoft9.png)

#### Can I run many services in one Container?

Yes, but we suggest you just run one service in one Container for the serverless infrastructure

#### Can use visit my application of Docker from Internet?

If you have completed the port mapping for Server with Docker, use can visit from Internet

#### If there have Docker GUI tools?

Yes, we suggest you use Portainer which is very popular

#### Can I modify the root directory of Docker?

Yes, but we don't recommend to modify it

#### What's the difference between Deployment and Installation?

- Deployment is a process of installing and configuring a sequence of software in sequence in a different order, which is a complex system engineering.  
- Installation is the process of starting the initial wizard after the application is prepared.  
- Installation is simpler than deployment. 

#### What's Cloud Platform?

Cloud platform refers to platform manufacturers that provide cloud computing services, such as: **Azure, AWS, Alibaba Cloud, HUAWEI CLOUD, Tencent Cloud**, etc.

#### What is the difference between Instance, Cloud Server, Virtual Machine, ECS, EC2, CVM, and VM?

No difference, just the terminology used by different manufacturers, actually cloud servers