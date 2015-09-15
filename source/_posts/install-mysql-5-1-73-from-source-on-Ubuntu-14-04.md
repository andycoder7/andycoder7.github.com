title: "install mysql 5.1.73 from source on Ubuntu 14.04"
date: 2015-09-15 09:56:48
tags: [mysql]
categories: 琐碎
---

## 0x00 install Ubuntu 14.04 and change the apt source
    
` sudo vi /etc/apt/sources.list`

> deb http://debian.ustc.edu.cn/ubuntu/ trusty main multiverse restricted universe  
> deb http://debian.ustc.edu.cn/ubuntu/ trusty-backports main multiverse restricted universe  
> deb http://debian.ustc.edu.cn/ubuntu/ trusty-proposed main multiverse restricted universe  
> deb http://debian.ustc.edu.cn/ubuntu/ trusty-security main multiverse restricted universe  
> deb http://debian.ustc.edu.cn/ubuntu/ trusty-updates main multiverse restricted universe  
> deb-src http://debian.ustc.edu.cn/ubuntu/ trusty main multiverse restricted universe    
> deb-src http://debian.ustc.edu.cn/ubuntu/ trusty-backports main multiverse restricted universe  
> deb-src http://debian.ustc.edu.cn/ubuntu/ trusty-proposed main multiverse restricted universe  
> deb-src http://debian.ustc.edu.cn/ubuntu/ trusty-security main multiverse restricted universe  
> deb-src http://debian.ustc.edu.cn/ubuntu/ trusty-updates main multiverse restricted universe  
    
<!-- more -->

## 0x01 build mysql 5.1.73

```
sudo apt-get update
sudo apt-get install -y build-essential
sudo apt-get install -y libncurses5-dev
sudo useradd mysql

cd
wget http://dev.mysql.com/get/Downloads/MySQL-5.1/mysql-5.1.73.tar.gz
tar xzvf mysql-5.1.73.tar.gz
cd mysql-5.1.73

./configure \
        '--prefix=/usr' \
        '--exec-prefix=/usr' \
        '--libexecdir=/usr/sbin' \
        '--datadir=/usr/share' \
        '--localstatedir=/var/lib/mysql' \
        '--includedir=/usr/include' \
        '--infodir=/usr/share/info' \
        '--mandir=/usr/share/man' \
        '--with-system-type=debian-linux-gnu' \
        '--enable-shared' \
        '--enable-static' \
        '--enable-thread-safe-client' \
        '--enable-assembler' \
        '--enable-local-infile' \
        '--with-fast-mutexes' \
        '--with-big-tables' \
        '--with-unix-socket-path=/var/run/mysqld/mysqld.sock' \
        '--with-mysqld-user=mysql' \
        '--with-libwrap' \
        '--with-readline' \
        '--with-ssl' \
        '--without-docs' \
        '--with-extra-charsets=all' \
        '--with-plugins=max' \
        '--with-embedded-server' \
        '--with-embedded-privilege-control'

make
sudo make install

sudo mkdir -p /etc/mysql
sudo mkdir -p /var/lib/mysql
sudo mkdir -p /etc/mysql/conf.d
sudo echo -e '[mysqld_safe]\nsyslog' > /etc/mysql/conf.d/mysqld_safe_syslog.cnf
sudo cp /usr/share/mysql/my-medium.cnf /etc/mysql/my.cnf
sudo sed -i 's#.*datadir.*#datadir = /var/lib/mysql#g' /etc/mysql/my.cnf
sudo chown mysql:mysql -R /var/lib/mysql

sudo -u mysql mysql_install_db --user=mysql --datadir=/var/lib/mysql
sudo -u mysql mysqld_safe --user=mysql &
/usr/bin/mysql_secure_installation

sudo cp /usr/share/mysql/mysql.server /etc/init.d/mysql
sudo chmod +x /etc/init.d/mysql
sudo update-rc.d mysql defaults
```

