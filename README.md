
# AWS WordPress

## Overview
Install Wordpress on LAMP Server

## Prerequisite
- [LAMP Server](https://github.com/miihirsawant-git/aws-lamp-server)
- [Domain Name](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-to-ec2-instance.html)
    

## Installation
-   Configure your [Domain Name]((https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-to-ec2-instance.html)) to point to the LAMP server. You need to set up at least `two A records` (www, @) in your domain name to have them point to the LAMP instance.
-   Download and Extract Wordpress
```
cd /var/www/
curl https://wordpress.org/latest.tar.gz --output wordpress.tar.gz
sudo tar -xf wordpress.tar.gz
```
- By default, the directory permissions would not be set. You need to configure the owner for the WordPress directory as the web server, along with setting up the `file permissions`.
```
sudo chown -R nginx:nginx /var/www/wordpress
sudo find /var/www/wordpress -type d -exec chmod 755 {} \;
sudo find /var/www/wordpress -type f -exec chmod 644 {} \;
```
-   Create and open a `WordPress configuration` file
```
sudo touch /etc/httpd/conf.d/wordpress.conf
sudo vi /etc/httpd/conf.d/wordpress.conf
```
-   Edit the configuration file to include the Domain Name Server details for WordPress to follow
```
<VirtualHost *:80>
    ServerName <Your Domain Name Server >
    ServerAlias <Your Domain Name Server - DNS A record>
    DocumentRoot /var/www/wordpress
    <Directory "/var/www/wordpress">
        Options Indexes FollowSymLinks
        AllowOverride all
        Require all granted
    </Directory>
    ErrorLog /var/log/httpd/wordpress_error.log
    CustomLog /var/log/httpd/wordpress_access.log common
</VirtualHost>
```
- Restart apache server to apply saved configuration
```
sudo systemctl restart httpd
```
- Next up, set up the wordpress database for the  application to folow with command (Enter `root` password for the database instead of OS)
```
mysql -u root -p
```
- Create a `WordPress database` with DB commands
```
create database db_wordpress;
create user ‘wordpress_user’@‘localhost' identified by ‘wordpress_pw';
grant all privileges on db_wordpress.* TO 'wordpress_user'@'localhost';
flush privileges;
exit
```
- Install Dependencies: `PHP modules`
```
sudo dnf install php-mysqlnd php-gd php-json php-mbstring php-pecl-zip php-xml php-mysqli -y
```
- Allow Apache to work with `SELinux`
```
sudo chcon -R -t httpd_sys_rw_content_t /var/www/wordpress/
sudo setsebool httpd_can_network_connect true
```
- Finish Wordpress installation
```
http://<Your Domain Name Server>/wp-admin/
```
 
## Completion
That’s it. Your Wordpress application is up and running!


