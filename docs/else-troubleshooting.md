# Troubleshooting

We collect the most common troubleshooting of using Docker for your reference:

> Many troubleshooting is closely related to the Server, if you can confirm troubleshooting is related to Cloud Platform, please refer to [Cloud Platform Documentation](https://support.websoft9.com/docs/faq/tech-instance.html)

#### The container fails to start?

The most common reason is that the user did not set the required environment variables according to the requirements of the container, causing the container to fail to start

#### Container port mapping failed?

If the user is unable to grasp the mapping relationship and availability between the host (server) operating system port and the container port, please enable automatic port mapping

#### MySQL can't be connected by remote?

There are three possible reasons for this problem:

1. The port mapping is set incorrectly, causing the container to have no network
2. The container does not have remote access
3. Special requirements for MySQL 8.0
