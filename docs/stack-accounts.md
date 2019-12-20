# Username and Password

These accounts are required for Docker image installation and configuration

## Docker

Following is the default Administrator account of Docker: 

Administrator username: `Admin`  
Administrator password: `zabbix`  

Docker log in URL: *http://Internet IP/zabbix*

## MariaDB

* Administrator username: *`root`*
* Administrator password: stored in the file of your server instance: */credentials/password.txt*. 
   ![Run the cat command](https://libs.websoft9.com/Websoft9/DocsPicture/zh/common/catdbpassword-websoft9.png)

   You use the **online SSH interface on Cloud Console** and run `cat /credentials/password.txt` command to get the database password

> If you want to log in MySQL, refer to [MySQL Web interface Management](/admin-mysql.md)

## Linux

* Host Name: Internet IP or Public IP of your Instance
* Connect by: Online SSH on Cloud Console or SFTP/SSH tools on your local computer
* Password: It was set by yourself when created instance
* Username: Different Cloud Platform has differences
   |  Cloud Platform   |  Administrator Username   | Other |
   | --- | --- | --- |
   |  Azure   |  It was set by yourself when created instance   | [How to enable root access?](https://support.websoft9.com/docs/azure/server-login.html#sample2-enable-the-root-username) |
   |  AWS CentOS   |  centos   | [How to enable root access?](https://support.websoft9.com/docs/aws/server-login.html#sample2-enable-the-root-username) |
   |  AWS Ubuntu   |  ubuntu   | [How to enable root access?](https://support.websoft9.com/docs/aws/server-login.html#sample2-enable-the-root-username) |
   |  Alibaba Cloud, HUAWEI CLOUD, Tencent Cloud |  root   |

If don't remember the password of Linux, you should reset password on Cloud Console