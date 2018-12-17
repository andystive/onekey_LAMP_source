# onekey_LAMP_source
#源码包安装lamp脚本
-------------------------------------------------------------------
#!/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
export PATH

####################################################################
##      本脚本为一键部署Apache、Mariadb、PHP、wordpress             ##
####################################################################

#检测当前用户是否为root
if [ "$(whoami)" == "root" ];then
	echo "当前用户为root，将进行部署"
else
	echo "请更换root用户进行操作"
	exit 1
fi

#更新系统并安装相关依赖包
yum install -y epel-release
yum update -y
yum install -y dos2unix
yum install -y wget
yum unstall -y cmake
yum install -y gcc gcc-c++ autoconf libtool

#安装MySQL依赖包
yum install -y readline-devel zlib-devel openssl-devel libaio-devel bison git

#安装PHP依赖包
yum install -y php-mcrypt libmcrypt libmcrypt-devel  libxml2-devel  openssl-devel  libcurl-devel libjpeg.x86_64 libpng.x86_64 freetype.x86_64 libjpeg-devel.x86_64 libpng-devel.x86_64 freetype-devel.x86_64  libjpeg-turbo-devel   libmcrypt-devel   mysql-devel

#定义源码包及程序安装路径
#源码包存放目录
S_DIR=/usr/local/src

#定义Apr安装路径
HA_URL=http://mirrors.shu.edu.cn/apache//apr/apr-1.6.5.tar.gz
HA_FILES=apr-1.6.5.tar.gz
HA_FILES_DIR=apr-1.6.5
HA_PREFIX=/usr/local/apr

#定义apr-util安装路径
HU_URL=http://mirrors.shu.edu.cn/apache//apr/apr-util-1.6.1.tar.gz
HU_FILES=apr-util-1.6.1.tar.gz
HU_FILES_DIR=apr-util-1.6.1
HU_PREFIX=/usr/local/apr-util

#定义pcre安装路径
HP_URL=https://ftp.pcre.org/pub/pcre/pcre-8.42.tar.gz
HP_FILES=pcre-8.42.tar.gz
HP_FILES_DIR=pcre-8.42
HP_PREFIX=/usr/local/pcre

#定义Apache安装路径
H_URL=http://mirrors.tuna.tsinghua.edu.cn/apache//httpd/httpd-2.4.35.tar.gz
H_FILES=httpd-2.4.35.tar.gz
H_FILES_DIR=httpd-2.4.35
H_PREFIX=/usr/local/apache

#定义Mariadb安装路径
M_URL=https://mirrors.shu.edu.cn/mariadb//mariadb-10.3.10/source/mariadb-10.3.10.tar.gz
M_FILES=mariadb-10.3.10.tar.gz
M_FILES_DIR=mariadb-10.3.10
M_PREFIX=/usr/local/mysql

#定义PHP安装路径
P_URL=http://cn.php.net/distributions/php-7.2.11.tar.gz
P_FILES=php-7.2.11.tar.gz
P_FILES_DIR=php-7.2.11
P_PREFIX=/usr/local/php

#定义phpMyadmin变量
PM_URL=https://files.phpmyadmin.net/phpMyAdmin/4.8.3/phpMyAdmin-4.8.3-all-languages.tar.gz
PM_FILES=phpMyAdmin-4.8.3-all-languages.tar.gz
PM_FILES_DIR=phpMyAdmin-4.8.3-all-languages

#定义Wordpress变量
W_URL=https://cn.wordpress.org/wordpress-4.9.4-zh_CN.tar.gz
W_FILES=wordpress-4.9.4-zh_CN.tar.gz
W_FILES_DIR=wordpress


#开启防火墙80端口
firewall-cmd --zone=public --add-port=80/tcp --permanent
#重启防火墙
systemctl restart firewalld.service

##########下载Apache、MySQL、PHP、wordpress源码包##########
cd $S_DIR
if [ -e $HA_FILES ]; then
	echo "$HA_FILES 已存在，无须下载"
else
	wget -c $HA_URL
fi

if [ -e $HU_FILES ]; then
	echo "$HU_FILES 已存在，无须下载"
else
	wget -c $HU_URL
fi

if [ -e $HP_FILES ]; then
	echo "$HP_FILES 已存在，无须下载"
else
	wget -c $HP_URL
fi

if [ -e $H_FILES ]; then
	echo "$H_FILES 已存在，无须下载"
else
	wget -c $H_URL
fi

if [ -e $M_FILES ]; then
	echo "$M_FILES 已存在，无须下载"
else
	wget -c $M_URL
fi

if [ -e $P_FILES ]; then
	echo "$P_FILES 已存在，无须下载"
else
	wget -c $P_URL
fi

if [ -e $PM_FILES ]; then
	echo "$PM_FILES 已存在，无须下载"
else
	wget -c $PM_URL
fi

if [ -e $W_FILES ]; then
	echo "$W_FILES 已存在，无须下载"
else
	wget -c $W_URL
fi



###########开始部署LAMP#########
#安装apr
cd $S_DIR
tar -zxvf $HA_FILES
cd $HA_FILES_DIR
./configure --prefix=$HA_PREFIX
if [ $? -eq 0 ]; then
	make && make install
	echo "$HA_FILES 安装成功！"
else
	echo "$HA_FILES 安装失败，请检查！"
fi

#安装apr-util
cd $S_DIR
tar -zxvf $HU_FILES
cd $HU_FILES_DIR
./configure --prefix=$HU_PREFIX --with-apr=$HA_PREFIX
if [ $? -eq 0 ]; then
	make && make install
	echo "$HU_FILES 安装成功！"
else
	echo "$HU_FILES 安装失败，请检查！"
fi

#安装pcre
cd $S_DIR
tar -zxvf $HP_FILES
cd $HP_FILES_DIR
./configure --prefix=$HP_PREFIX
if [ $? -eq 0 ]; then
	make && make install
	echo "$HP_FILES 安装成功！"
else
	echo "$HP_FILES 安装失败，请检查！"
fi


#安装Apache
cd $S_DIR
tar -zxvf $H_FILES
cd $H_FILES_DIR
./configure
--prefix=$H_PREFIX \
--sysconfdir=/etc/httpd \
--enable-so \
--enable-cgi \
--enable-rewrite \
--with-zlib \
--with-pcre=$HP_PREFIX \
--with-apr=$HA_PREFIX \
--with-apr-util=$HU_PREFIX \
--enable-mods-shared=most \
--enable-mpms-shared=all \
--with-mpm=event
if [ $? -eq 0 ]; then
	make && make install
	echo "$H_FILES 安装成功！"
else
	echo "$H_FILES 安装失败，请检查！"
fi


#卸载系统预安装的MySQL或MariaDB
rpm -e --nodeps mysql*
rpm -e --nodeps mariadb*

#安装Mariadb
cd $S_DIR
tar -xzvf $M_FILES
cd $M_FILES_DIR
cmake .
-DCMAKE_INSTALL_PREFIX=$M_PREFIX \
-DMYSQL_DATADIR=$M_PREFIX/data \
-DSYSCONFDIR=/etc \
-DWITHOUT_TOKUDB=1 \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_ARCHIVE_STPRAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DWIYH_READLINE=1 \
-DWIYH_SSL=system \
-DVITH_ZLIB=system \
-DWITH_LOBWRAP=0 \
-DMYSQL_UNIX_ADDR=/tmp/mysql.sock \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci
if [ $? -eq 0 ]; then
	make && make install
	echo "$M_FILES 安装成功！"
else
	echo "$M_FILES 安装失败，请检查！"
fi

##安装PHP
cd $S_DIR
tar -zxvf $P_FILES
cd $P_FILES_DIR
./configure
--prefix=$P_PREFIX \
--enable-mysqlnd \
--with-mysqli=mysqlnd \
--with-openssl \
--with-pdo-mysql=mysqlnd \
--enable-mbstring \
--with-freetype-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib \
--with-libxml-dir=/usr \
--enable-xml  \
--enable-sockets \
--with-apxs2=$H_PREFIX/bin/apxs \
--with-mcrypt  \
--with-config-file-path=/etc \
--with-config-file-scan-dir=/etc/php.d \
--enable-maintainer-zts \
--disable-fileinfo
if [ $? -eq 0 ]; then
	make && make install
	echo "$P_FILES 安装成功！"
else
	echo "$P_FILES 安装失败，请检查！"
fi


#安装phpMyAdmin
mkdir -p $H_PREFIX/htdocs/phpmyadmin
cd $S_DIR
tar -zxvf phpMyAdmin-4.1.8-all-languages.tar.gz
mv phpMyAdmin-4.1.8-all-languages/*  $H_PREFIX/htdocs/phpmyadmin

#安装wordpress
mkdir -p $H_PREFIX/htdocs/wordpress
cd $S_DIR
tar -zxvf wordpress-4.9.4-zh_CN.tar.gz
mv wordpress/* $H_PREFIX/htdocs/wordpress

##############配置相关参数#######################

#设置Apache、MySQL环境变量
sed -i "s%PATH=\$PATH:\$HOME/bin%PATH=\$PATH:\$HOME/bin:$H_PREFIX/bin:$M_PREFIX/bin%" /root/.bash_profile
source /root/.bash_profile

#设置Apache、MySQL开机自启动
cp $M_PREFIX/support-files/my-medium.cnf /etc/my.cnf
cp $M_PREFIX/support-files/mysql.server  /etc/init.d/mysqld
chmod +x /etc/init.d/mysqld

cat >>/etc/rc.d/rc.local <<EOF
/etc/init.d/mysqld start
$H_PREFIX/bin/apachectl start
EOF
chmod +x /etc/rc.d/rc.local

#建立mysql组和用户，并将mysql用户添加到mysql组
groupadd mysql
useradd -g mysql -s /sbin/nologin mysql

#更改MySQL安装目录的属性
chown -R mysql:mysql $M_PREFIX

#初始化MySQL数据库
$M_PREFIX/bin/mysqld --initialize-insecure --datadir=$M_PREFIX/data/ --user=mysql

#启动mysql服务
/etc/init.d/mysqld start

#修改httpd.conf配置参数
sed -i 's/Require all denied/Require all granted/' /etc/httpd/httpd.conf
sed -i 's/#ServerName www.example.com:80/ServerName localhost:80/' /etc/httpd/httpd.conf
sed -i '$a PidFile "/var/run/httpd.pid"' /etc/httpd/httpd.conf
sed -i 's/User daemon/User apache/' /etc/httpd/httpd.conf
sed -i 's/Group daemon/Group apache/' /etc/httpd/httpd.conf

#编辑Apache配置文件 httpd.conf，以Apache支持PHP
sed -i '$a AddType application/x-httpd-php  .php' /etc/httpd/httpd.conf
sed -i '$a AddType application/x-httpd-php-source  .phps' /etc/httpd/httpd.conf
sed -i 's/DirectoryIndex index.html/DirectoryIndex index.php index.html/' /etc/httpd/httpd.conf

#执行MySQL安全配置向导
echo 1、为root用户设置密码
echo 2、删除匿名账号
echo 3、取消root用户远程登录
echo 4、删除test库和对test库的访问权限
echo 5、刷新授权表使修改生效
$M_PREFIX/bin/mysql_secure_installation

#创建wordpress数据库
read -s -p "请输入数据库root密码：" passwd

HOSTNAME="localhost"
PORT="3306"
USERNAME="root"
PASSWORD="$passwd"
DBNAME="wordpress"

create_db_sql="create database IF NOT EXISTS ${DBNAME}"
mysql -h${HOSTNAME} -P${PORT} -u${USERNAME} -p${PASSWORD} -e"${create_db_sql}"

#修改wordpress配置，去除ftp登录请求
cd $H_PREFIX/htdocs/wordpress
dos2unix -k -n wp-config-sample.php wp-config.php
sed -i '$a define("FS_METHOD","direct");' $H_PREFIX/htdocs/wordpress/wp-config.php
sed -i '$a define("FS_CHMOD_DIR",0777);' $H_PREFIX/htdocs/wordpress/wp-config.php
sed -i '$a define("FS_CHMOD_FILE",0777);' $H_PREFIX/htdocs/wordpress/wp-config.php
sed -i 's/database_name_here/wordpress/' $H_PREFIX/htdocs/wordpress/wp-config.php
sed -i 's/username_here/root/' $H_PREFIX/htdocs/wordpress/wp-config.php
sed -i "s/password_here/$passwd/" $H_PREFIX/htdocs/wordpress/wp-config.php

#复制PHP配置文件
cp $S_DIR/php-7.0.12/php.ini-production /etc/php.ini

#编辑index.php文件以验证是否安装成功
cat >$H_PREFIX/htdocs/index.php << EOF
<?php
phpinfo();
?>
EOF
#建立Apache组和用户，并将apache用户添加到apache组
groupadd apache
useradd -g apache -s /sbin/nologin apache

#更改Apache安装目录的属性
chown -R apache:apache $H_PREFIX

#启动Apache服务
$H_PREFIX/bin/apachectl start
