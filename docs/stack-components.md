# Parameters

The Docker deployment package contains a sequence software (referred to as "components") required for Docker to run. The important information such as the component name, installation directory path, configuration file path, port, version, etc. are listed below.

> Docker-Server, Docker-Agent, Docker-Web have included for this deployment solution

## Path

### Docker

Docker installation directory: */usr/share/zabbix*  
Docker configuration file: */usr/share/zabbix/conf/zabbix.conf.php*    
Docker-Agent logs file：*/var/log/zabbix/zabbix_agentd.log*     
Docker-Server logs file：*/var/log/zabbix/zabbix_server.log*  

> Docker configuration file includes the database connection information

### PHP

PHP configuration file: */etc/php/7.2/apache2/php.ini*  
PHP Modules configurations directory: */etc/php/7.2/mods-available*

### Apache

Apache vhost configuration file：*/etc/apache2/sites-available/000-default.conf*  
Apache main configuration file：*/etc/apache2/apache2.conf*  
Apache logs file：*/var/log/apache2*  
Apache Apache module configuration directory： */etc/apache2/mods-available*

### MariaDB

MySQL installation directory: */usr/local/mysql*  
MySQL data directory: */var/lib/mysql*  
MySQL configuration file: */etc/mysql/mariadb.conf.d/50-server.cnf*    

## Ports

You can control(open or shut down) ports by **[Security Group Setting](https://support.websoft9.com/docs/faq/tech-instance.html)** of your Cloud Server whether the port can be accessed from Internet.

These ports should be opened for this application:

| Name | Number | Use |  Necessity |
| --- | --- | --- | --- |
| MariaDB | 3306 | Remote connect MariaDB | Optional |
| HTTP | 80 | HTTP requests for Docker | Required |
| HTTPS | 443 | HTTPS requests Docker | Optional |

## Version

You can see the version from product page of Marketplace. However, after being deployed to your server, the components will be automatically updated, resulting in a certain change in the version number. Therefore, the exact version number should be viewed by running the command on the server:

```shell
# Linux Version
lsb_release -a

# PHP Version
php -v

# List Installed PHP Modules
php -m

# Apache version on Ubuntu
apache2 -v

# Apache version on Centos
httpd -v

# List Installed Apache Modules
apachectl -M

# MySQL version:
mysql -V
```