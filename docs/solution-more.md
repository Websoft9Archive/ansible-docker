# More

Each of the following solutions has been proven to be effective and we hope to be helpful to you.

## Domain binding

The precondition for **Domain binding** is have completed the **Domain resolution** for Docker Instance.

From the perspective of server security and subsequent maintenance considerations, the **Domain Binding** step cannot be omitted.

Docker domain name binding steps:

1. Connect your Cloud Server
2. Modify [vhost configuration file](/stack-components.md#apache), change the domain name item for you
   ```text
     <VirtualHost *:80>
     ServerName zabbix.mydomain.com # modify it for you
   ```
3. Save it and restart [Web Service](/admin-services.md#apache)


## Docker language

After implementation of Gettext for Docker 2.0 the locales of used languages have to be installed on web server. Some operating systems have installed almost all locales by default (for instance CentOS, FreeBSD).

Following is the steps for you to enable Chinese language:

1. Connect to Docker's server, run one of these command
   ```
   ##command one (Suggestion)
   locale-gen zh_CN.UTF-8

   ##command two
   dpkg-reconfigure locales
   ```

   If you use the **command two**, please refer the pictures below, selectd **zh_CN.UTF-8 UTF-8** locale(Keyboard space bar selected, Tab key to switch position)

   ![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/zabbix/zabbix-localescn-websoft9.png)
   ![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/zabbix/zabbix-localescndef-websoft9.png)
   

2. Restart the Apache service
   ```
   service apache2 restart
   ```

3. Login to Docker console, refresh the lanuage page, you can use Chinese lanuage now
   ![Docker change language](https://libs.websoft9.com/Websoft9/DocsPicture/en/zabbix/zabbix-changelang-websoft9.png)

More details refer to [https://zabbix.org/wiki/How_to/install_locale](https://zabbix.org/wiki/How_to/install_locale)