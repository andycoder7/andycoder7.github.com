title: "windows7上单独安装Apache+Mysql+PHP"
date: 2015-09-17 08:14:09
tags: [windows, Apache, Mysql, PHP]
categories: 琐碎
---

## 版本说明：

1. windows7 74bit
2. Apache 2.4.12
3. Mysql 5.6.26
4. PHP 5.6.12

## APACHE

- unrar x [Apache_HTTP_Server_2.4.12.rar](/uploads/install_amp_on_win/Apache_HTTP_Server_2.4.12.rar)  
- cp -r Apache\ HTTP\ Server\ /x64 apache_2_4_12
- vi apache_2_4_12/conf/httpd.conf
    - modify the SRVROOT as your apache dir, not web dir
    - modify the DocumentRoot as your web dir
- vi apache_2_4_12/conf/extra/httpd-vhosts.conf
    - modify the DocumentRoot as your web dir
- double click apache_2_4_12/bin/ApacheMonitor.exe and start apache

<!-- more -->

## PHP

- unzip [php-5.6.12-Win32-VC11-x64.zip](/uploads/install_amp_on_win/php-5.6.12-Win32-VC11-x64.zip)
- double click the [vcredist_x64.exe](/uploads/install_amp_on_win/vcredist_x64.exe) to install msvcr110.dll
- vi apache_2_4_12/conf/httpd.conf
    - ```
        #add php module
        LoadModule php5_module "c:/software/php_5_6_12/php5apache2_4.dll"
        PHPIniDir "c:/software/php_5_6_12"
        AddType application/x-httpd-php .php .html .htm ```
- restart the apache server
- add c:/software/php_5_6_12 into PATH

## MYSQL

- unzip [mysql-5.6.26-win64.zip]()
- mv mysql-5.6.26-win64 mysql_5_6_26
- cd mysql_5_6_26
- cp my-default.ini my.ini
- vi my.ini
    - ```
        basedir = c:/software/mysql_5_6_26
        datadir = c:/software/mysql_5_6_26/data
        innodb_data_home_dir = c:/software/mysql_5_6_26/data
        port = 3306
        character_set_server = utf8```
- add c:/software/mysql_5_6_26/bin into PATH
- cmd(need root)> `mysqld --install mysql --defaults-file="c:\software\mysql_5_6_26\my.ini"`
    - if the path about mysql service in services.msc is wrong. you can goto regedit to modefy the `\HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\mysql\ImagePath`
- cmd(need root)> `net start|stop mysql`
    - if there is a problem about 1067, you can find the err log in c:\software\mysql_5_6_26\data\charles-PC.err and if the err is can't open the ibdata1 with read&write.
        - you can stop the mysql and rm the ib_logfile0 and ib_logfile1 which in the same path with ibdata1
        - cmd(need root)> `mysqladmin -u root shutdown # stop mysql`
- cmd(need root)> `mysqld --remove # remove mysql service in services.msc`

## PHP-MYSQL

- cp php.ini-development php.ini
- vi php.ini
    - `extension_dir="c:/software/php_5_6_12/ext"`
    - remove the ";" before extension=php_gd2.dll and extension=php_mysql.dll

PS: don't need to cp libmysql.dll or php-mysql.dll ... to c:\windows\system32.  
these dll can be found by extension_dir and apache can find the php.ini by PHPIniDir  

## SSL

- download the CA cert file from http://curl.haxx.se/docs/caextract.html
- mv cacert.pem c:\software\cacert.pem
- vi php.ini
    - remove the ";" before extension=php_openssl.dll
    - `openssl.cafile=c:\software\cacert.pem`

## rewrite

- remove "#" before LoadModule rewrite_module modules/mod_rewrite.so

## 我的配置文件

- [httpd.conf](/uploads/install_amp_on_win/httpd.conf)
- [php.ini](/uploads/install_amp_on_win/php.ini)
- [my.ini](/uploads/install_amp_on_win/my.ini)

*** PS: all path in this article is just an example ***
