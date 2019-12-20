# Update & Upgrade

Updates and upgrades are one of the maintenance tasks for sytem. Programs that are not upgraded for a long time, like buildings that are not maintained for a long time, will accelerate aging and gradually lose functionality until they are unavailable.

You should know the differences between the terms **Update** and **Upgrade**([Extended reading](https://support.websoft9.com/docs/faq/tech-upgrade.html#update-vs-upgrade))
- Operating system patching is **Update**, Ubuntu16.04 to Ubuntu18.04 is **Upgrade**
- MySQL5.6.25 to MySQL5.6.30 is **Update**, MySQL5.6 to MySQL5.7 is **Upgrade**

For Docker maintenance, focus on the following two Update & Upgrade jobs

- Sytem update(Operating System and Running Environment) 
- Docker upgrade 

## System Update

Run an update command to complete the system update:

``` shell
#For Ubuntu&Debian
apt update && apt upgrade -y

#For Centos&Redhat
yum update -y
```
> This deployment package is preconfigured with a scheduled task for automatic updates. If you want to remove the automatic update, please delete the corresponding Cron

## Docker Update

It is very easy for updating Docker(e.g. 4.0.1 to 4.0.3):

```
## update all Docker components
sudo apt install --only-upgrade 'zabbix.*'

## update Docker server
sudo apt install --only-upgrade 'zabbix-server.*'

## update Docker agent 
sudo apt install --only-upgrade 'zabbix-agent.*'
```

## Docker Upgrade

Docker upgrade(e.g. 3.0 to 4.0) is difficult than update, more upgrade details please refer to official documentation: [Docker Upgrade](https://www.zabbix.com/documentation/4.0/zh/manual/installation/upgrade)