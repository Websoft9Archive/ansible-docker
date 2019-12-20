# SSL/HTTPS

Docker deployment package has installed the SSL module of Nginx and open Certificate Authority **[Let's Encrypt](https://letsencrypt.org/)** for you configure the HTTPS quickly and conveniently.

> In addition to the vhost configuration file, HTTPS settings do not need to modify any files in Nginx

## Quick configuration

### Free certificate

If you want to use a free certificate, just run the one command `certbot` on your instance to start the HTTPS deployment.

1. Connect your server, run the command
   ```
   sudo certbot
   ```
2. Set by it by the configuration wizard

### Commercial certificate

If you have applied for a commercial certificate, complete the HTTPS configuration in just three steps:

1. Upload your certificate to the directory of your instance: */data/cert* 
2. Edit the vhost configuration file: */etc/apache2/sites-available/default-ssl.conf* 
3. Modify ServerName, SSLCertificateFile, SSLCertificateKeyFile
   ``` text
   SSLCertificateFile	/etc/ssl/certs/ssl-cert-snakeoil.pem
   SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key   
   ```
5. Save it and [Restart Apache service](/admin-services.md#apache)

## Special Guide

For details on configuring HTTPS pre-conditions, HTTPS configuration segment templates, precautions, detailed steps, and troubleshooting, refer to the [HTTPS Special Guide](https://support.websoft9.com/docs/faq/tech-https.html#nginx) provided by Websoft9 