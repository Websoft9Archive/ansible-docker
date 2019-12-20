# 服务启停

使用由Websoft9提供的 Docker 部署方案，可能需要用到的服务如下：

### Docker-Server

```shell
systemctl start zabbix-server
systemctl restart zabbix-server
systemctl stop zabbix-server
systemctl status zabbix-server
```

### Docker-Agent

```shell
systemctl start zabbix-agent
systemctl restart zabbix-agent
systemctl stop zabbix-agent
systemctl status zabbix-agent
```

### Apache

```shell
##For Ubuntu&Debian
sudo systemctl start apache2
sudo systemctl stop apache2
sudo systemctl restart apache2
sudo systemctl status apache2

##For Centos&Redhat
sudo systemctl start httpd
sudo systemctl stop httpd
sudo systemctl restart httpd
sudo systemctl status httpd
```

### MariaDB

```shell
sudo systemctl start mysql
sudo systemctl stop mysql
sudo systemctl restart mysql
sudo systemctl status mysql
```