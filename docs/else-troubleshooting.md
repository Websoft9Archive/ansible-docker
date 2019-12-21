# Troubleshooting

We collect the most common troubleshooting of using Docker for your reference:

> Many troubleshooting is closely related to the Server, if you can confirm troubleshooting is related to Cloud Platform, please refer to [Cloud Platform Documentation](https://support.websoft9.com/docs/faq/tech-instance.html)

#### 容器无法启动？

最常见的原因是用户没有按照该容器的要求，设置必须的环境变量，导致容器启动失败

#### 容器端口映射失败？

如果用户无法把握宿主机（服务器）操作系统端口与容器端口的映射关系、可用性情况，请开启端口自动映射

#### MySQL 容器无法远程访问？

导致这个问题的可能原因有三点：

1. 端口映射设置错误，导致容器没有网络
2. 容器没有开启远程访问权限
3. MySQL 8.0 特殊设置要求
