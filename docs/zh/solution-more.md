# 更多...

下面每一个方案，都经过实践证明行之有效，希望能够对你有帮助

## 域名绑定

绑定域名的前置条件是：已经完成域名解析（登录域名控制台，增加一个A记录指向服务器公网IP）  

完成域名解析后，从服务器安全和后续维护考量，需要完成**域名绑定**：

Docker 域名绑定操作步骤：

1. 使用 SFTP 工具登录云服务器
2. 修改 [虚拟机主机配置文件](/zh/stack-components.md#apache)，将其中的域名相关的值
   ```text
     <VirtualHost *:80>
     ServerName www.example.com # 修改成您的实际域名
   ```
3. 保存配置文件，[重启 Apache 服务](/zh/admin-services.md#apache)

## Docker 多语言

Docker 默认已经内置多种语言包，但 Docker 的语言包依赖于操作系统对应的字符编码设置。

下面以开启中文字符编码为例进行详细说明：

1. 登录 Docker 所在的服务器，运行下面的命令之一
   ```
   ##方案一
   locale-gen zh_CN.UTF-8

   ##方案二
   dpkg-reconfigure locales
   ```

   如果运行**方案二**，请参考下图选择 **zh_CN.UTF-8 UTF-8** 编码规则（键盘空格键选定，Tab键切换位置）

   ![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/zabbix/zabbix-localescn-websoft9.png)
   ![](https://libs.websoft9.com/Websoft9/DocsPicture/zh/zabbix/zabbix-localescndef-websoft9.png)
   

2. 重启服务
   ```
   service apache2 restart
   ```

3. 此时重新登录 Docker 后台，中文语言由灰色变成了可选
   ![Docker 更换语言](https://libs.websoft9.com/Websoft9/DocsPicture/en/zabbix/zabbix-changelang-websoft9.png)

参考官方字符编码安装方案：[https://zabbix.org/wiki/How_to/install_locale](https://zabbix.org/wiki/How_to/install_locale)