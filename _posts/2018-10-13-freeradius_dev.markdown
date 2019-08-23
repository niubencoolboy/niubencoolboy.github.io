---
layout: post
title: "对FreeRadius二次开发整套系统搭建"
date: 2018-10-13 20:00:00
author: "Johan Niu"
header-img : "img/post-bg-os-metro.jpg"
tags:
    - C/C++
    - 安全
       
---

# 对FreeRadius二次开发整套系统搭建

系统：centos 7.2

## 一. Centos 系统配置
### 1.1 修改root密码

`passwd root`

### 1.2 添加用户
	adduser niu
	passwd niu 

添加用户组，是新建用户有sudo权限：

`gpasswd -a niu wheel`

### 1.3 修改ssh配置

	vi /etc/ssh/sshd_config
	
	#PermitRootLogin yes -> no
	
退出重新登录，验证。

`sudo yum update`

**参考：**[https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7]()

## 二. 安装mysql
### 2.1 添加MySQL Yum Repository
centos 默认安装的是MariaDB，为安装MySQL，需要进入the MySQL community Yum Repository （[https://dev.mysql.com/downloads/repo/yum/]()），选择第一个Linux 7 获取下载链接。

`wget https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm`

`sudo rpm -ivh mysql80-community-release-el7-1.noarch.rpm`

该命令会在/etc/yum.repo.d/目录下产生一个mysql-community.repo文件，里面包含MySQL的yum地址，这样我们就可以使用yum进行安装了。

### 2.2 选择版本

	yum -y install yum-utils
	
	sudo yum-config-manager --disable mysql80-community
	
	sudo yum-config-manager --enable mysql57-community
	
	sudo yum repolist all | grep mysql

	mysql-cluster-7.5-community/x86_64 MySQL Cluster 7.5 Community      禁用
	mysql-cluster-7.5-community-source MySQL Cluster 7.5 Community - So 禁用
	mysql-cluster-7.6-community/x86_64 MySQL Cluster 7.6 Community      禁用
	mysql-cluster-7.6-community-source MySQL Cluster 7.6 Community - So 禁用
	mysql-connectors-community/x86_64  MySQL Connectors Community       启用:     74
	mysql-connectors-community-source  MySQL Connectors Community - Sou 禁用
	mysql-tools-community/x86_64       MySQL Tools Community            启用:     74
	mysql-tools-community-source       MySQL Tools Community - Source   禁用
	mysql-tools-preview/x86_64         MySQL Tools Preview              禁用
	mysql-tools-preview-source         MySQL Tools Preview - Source     禁用
	mysql55-community/x86_64           MySQL 5.5 Community Server       禁用
	mysql55-community-source           MySQL 5.5 Community Server - Sou 禁用
	mysql56-community/x86_64           MySQL 5.6 Community Server       禁用
	mysql56-community-source           MySQL 5.6 Community Server - Sou 禁用
	mysql57-community/x86_64           MySQL 5.7 Community Server       启用:    307
	mysql57-community-source           MySQL 5.7 Community Server - Sou 禁用
	mysql80-community/x86_64           MySQL 8.0 Community Server       禁用
	mysql80-community-source           MySQL 8.0 Community Server - Sou 禁用

### 2.3 安装、启动与修改root密码
安装命令

	sudo yum install mysql-community-server

启动服务

	sudo systemctl start mysqld
	
修改root密码，首先生成临时密码：

	[niu@johan~]$ sudo grep 'temporary password' /var/log/mysqld.log
	2018-11-09T02:40:27.582158Z 1 [Note] A temporary password is generated for root@localhost: w&P1RtkxW4??
	
然后进入数据库，修改root密码：

	mysql -uroot -p
	
	ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';

可能会出现error:

	ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
	
密码太简单，不服务 MySQL 5.7 密码规则。搞复杂点就行啦。

**参考：**

1. [https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-centos-7]()
2. [https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/]()


## 三. 安装freeradius
### 3.1 安装依赖包：

	sudo yum groupinstall "Development Tools" -y
	sudo yum -y install httpd httpd-devel
	
	sudo systemctl start httpd
	sudo systemctl status httpd

### 3.2 安装freeradius

	sudo yum -y install freeradius freeradius-utils freeradius-mysql
	
### 3.3 创建radius数据库

创建radiusdb数据库并设置本地、远程访问权限与密码

`mysql -u root -p`

	MariaDB [(none)]> create database radiusdb;
	<!--Query OK, 1 row affected (0.000 sec)-->
	
	MariaDB [(none)]> grant all on radiusdb.* TO radiususer@localhost identified by "RadiusPasswd@66";
	<!--Query OK, 0 rows affected (0.000 sec)-->
	
	MariaDB [(none)]> grant all on radiusdb.* TO radiususer@'%' identified by "RadiusPasswd@66";
	<!--Query OK, 0 rows affected (0.000 sec)-->
	
	MariaDB [(none)]> flush privileges;
	<!--Query OK, 0 rows affected (0.000 sec)-->
	
### 3.4 导入数据库schema
进入超级用户：

	sudo -i
	mysql -u root -p radiusdb < /etc/raddb/mods-config/sql/main/mysql/schema.sql

其他如果raddb目录不在/etc/raddb下，可以使用`radiusd --help`查看raddb目录在哪，

	[root@johan ~]# radiusd --help
	radiusd: invalid option -- '-'
	Usage: radiusd [options]
	Options:
	  -C            Check configuration and exit.
	  -d <raddb>    Set configuration directory (defaults to /usr/local/etc/raddb).
	  -D <dictdir>  Set main dictionary directory (defaults to /usr/local/share/freeradius).
	  -f            Run as a foreground process, not a daemon.
	  -h            Print this help message.
	  -l <log_file> Logging output will be written to this file.
	  -n <name>     Read raddb/name.conf instead of raddb/radiusd.conf.
	  -P            Always write out PID, even with -f.
	  -s            Do not spawn child processes to handle requests (same as -ft).
	  -t            Disable threads.
	  -T            Prepend timestamps to  log messages.
	  -v            Print server version information.
	  -X            Turn on full debugging (similar to -tfxxl stdout).
	  -x            Turn on additional debugging (-xx gives more debugging).

找到schema.sql文件，导入数据库：
 
	cd /usr/local/etc/raddb
	cd /usr/local/etc/raddb/mods-config/sql/main/mysql/
	mysql -u root -p radiusdb < schema.sql
	
### 3.5 配置freeradius使用MySQL

启用sql模块：

	ln -s /etc/raddb/mods-available/sql /etc/raddb/mods-enabled/
	chgrp -h radiusd /etc/raddb/mods-enabled/sql
	
配置mysql数据库连接信息：

	vi /etc/raddb/mods-available/sql	

主要修改内容如下：

	sql {
		 (...省略...）
	        
	    driver = "rlm_sql_mysql"
	    dialect = "mysql"
	        
	    # Connection info:
	    #
	    server = "localhost"
	    port = 3306
	    login = "dbuser"
	    password = "mypass"
	    radius_db = "radiusdb"
	    (...省略...）
	}

### 3.6 调整FreeRadius与MySql的启动顺序

添加FreeRadius启动服务：

	systemctl enable radiusd.service
 
 如果有报警，重置服务：
 
	systemctl daemon-reload

FreeRadius服务必须在数据库正常启动后才能正常启动，否则会出错。为了确保这一点，按照以下方法强制指定radius服务启动的顺序：

	vi /etc/systemd/system/multi-user.target.wants/radiusd.service
	
在[Unit]部分，增加After=mysqlb.service，如下所示：
	
	Unit]
	Description=FreeRADIUS high performance RADIUS server.
	After=syslog.target network.target ipa.service dirsrv.target krb5kdc.service
	After=mysqld.service
	
### 3.7 添加客户端连接设置
编辑`vi /etc/raddb/clients.conf`文件
	
主要修改共享秘钥，例如修改成123456

	client localhost {
	    ipaddr = 127.0.0.1
	    proto = *
	    secret = 123456
	}
    
自定义网段：

	client baoleij {
	    ipaddr = 172.20.15.0/24
	    secret = 123456
	    require_message_authenticator = no
	}

任意客户端连接：

	client all_client {
    ipaddr = 0.0.0.0/0
    secret = demo_radius_secret
    require_message_authenticator = no
	}
	
### 3.8 启动服务

	systemctl start radiusd.service
	systemctl status radiusd.service
	
### 3.9 测试radius

进入数据库 

`mysql -u root -p`，

`use radiusdb;`

使用sql添加一条测试记录：

`INSERT INTO radcheck (id, username, attribute, op, value) VALUES (1,'testuser','Cleartext-Password',':=','mypass');`
	
使用radtest命令测试：radtest **[用户名]** **[密码]** **[radius服务器host/ip]** 0 **[对客户端设置的共享秘钥]**，例如：

	`radtest testuser mypass localhost 0 testing123`
	(0) Error parsing "stdin": Failed resolving "bj-sg-johan-test" to IPv4 address: Name or service not known	
	
无法解析主机名地址，查看`/etc/hosts`文件；将主机名添加到localhost一行。如下：

	[root@bj-sg-johan-test ~]# cat /etc/hosts
	127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 bj-sg-johan-test
	::1         localhost localhost.localdomain localhost6 localhost6.localdomain6 
	
	
再测试：

	radtest testuser mypass localhost 0 testing123

结果：

	Sending Access-Request Id 143 from 0.0.0.0:51053 to 127.0.0.1:1812
	User-Name = 'testuser'
	User-Password = 'mypass'
	NAS-IP-Address = 127.0.0.1
	NAS-Port = 0
	Message-Authenticator = 0x00
	Received Access-Accept Id 143 from 127.0.0.1:1812 to 127.0.0.1:51053 length 26
	Acct-Interim-Interval = 60

测试结束后重新进入数据库，使用sql命令清除测试用户：

`delete from radcheck where username = 'testuser';`
	
### 3.10 debug radius	
	
如果radius启动出现问题，可以将radius进程停止，然后以参数-X的方式临时启动调试模式。

	systemctl stop radiusd.service
	# pkill radius   
	# radiusd -X

结果：

	（省略。。）
	Listening on auth address * port 1812 bound to server default
	Listening on acct address * port 1813 bound to server default
	Listening on auth address :: port 1812 bound to server default
	Listening on acct address :: port 1813 bound to server default
	Listening on auth address 127.0.0.1 port 18120 bound to server inner-tunnel
	Listening on proxy address * port 38607
	Listening on proxy address :: port 50724
	Ready to process requests
	
## 四 安装 PHP

### 4.1 导入remi源（包含最新版的PHP7）
	
	rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

注意：remi源的默认设置就是enable=0，所以使用时必须增加--enablerepo参数调用。
	
### 4.2 yum安装PHP7及常用的模块
	
	yum install --enablerepo=epel,remi-php70 php php-mbstring php-pear php-fpm php-mcrypt php-mysql php-gd php-xml 
	    
	pear install DB

### 4.3 确认版本
	
	php -v
	
	PHP 7.0.32 (cli) (built: Sep 11 2018 13:20:19) ( NTS )
	Copyright (c) 1997-2017 The PHP Group
	Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
	
## 五 安装 Nginx
	
### 5.1 安装nginx

	# yum --enablerepo=epel install nginx
	
查看版本：

	[root@bj-sg-johan-test ~]# nginx -V
	nginx version: nginx/1.12.2
	built by gcc 4.8.5 20150623 (Red Hat 4.8.5-16) (GCC)
	built with OpenSSL 1.0.2k-fips  26 Jan 2017
	TLS SNI support enabled
	configure arguments: --prefix=/usr/share/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf .......	

### 5.2 设置nginx自动启动

	# systemctl enable nginx


## 六 配置php-fpm服务

### 6.1 修改php-fpm的默认设置

上一步安装的php-fpm，默认设置里运行用户都是apache，需要先修改成nginx

`vi /etc/php-fpm.d/www.conf`

需要修改的内容：

	user = nginx
	group = nginx
	listen.owner = nginx
	listen.group = nginx
  
    listen = /var/run/php-fpm/php-fpm.sock
    
### 6.2 启动php-fpm服务

	# systemctl start php-fpm
	# systemctl enable php-fpm
	
## 七 安装daloRADIUS（radius web managent system） 
	
daloRADIUS的官网：[http://www.daloradius.com/]()

### 7.1 下载并解压daloRADIUS

	mkdir -p /opt/www
	cd /opt/www
	wget https://github.com/lirantal/daloradius/archive/master.zip
	unzip master.zip
	mv daloradius-master/ daloradius
	chown -R nginx:nginx daloradius

### 7.2 导入daloRADIUS扩展表

由于daloRADIUS除了用到了基本的FreeRadius表，例如radcheck, radreply等，还扩展了很多附件表，例如billing_history,userinfo等，因此首先需要导入这些扩展表。

	cd /opt/www/daloradius
	mysql -u root -p radiusdb < contrib/db/mysql-daloradius.sql
	mysql -u root -p radius < contrib/db/fr2-mysql-daloradius-and-freeradius.sql

### 7.3 配置数据库连接

	chmod 664 /opt/www/daloradius/library/daloradius.conf.php
	vi /opt/www/daloradius/library/daloradius.conf.php
	
主要需要修改的是数据库的连接信息：

	$configValues['CONFIG_DB_HOST'] = 'localhost';
	$configValues['CONFIG_DB_PORT'] = '3306';
	$configValues['CONFIG_DB_USER'] = 'radiususer';
	$configValues['CONFIG_DB_PASS'] = 'RadiusPasswd@66';
	$configValues['CONFIG_DB_NAME'] = 'radiusdb';

### 7.4 配置网站
在 `/etc/nginx/conf.d/` 目录下创建 daloradius.conf 配置文件。配置如下：

查看端口是否占用：

	#查看tcp端口使用情况
	netstat -nltp
	
	#查看udp端口使用情况
	netstat -nlup
	
	
	server {
	    listen 8080;
	    server_name daloradius.com;
	    root /opt/www/daloradius;
	    index index.php;
	         
	    charset utf-8;
	         
	    try_files $uri $uri/ /index.php?q=$uri&$args;
	            
	    location ~ \.php$ {
	        fastcgi_pass unix:/var/run/php-fpm/php-fpm.sock;
	        fastcgi_index index.php;
	        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
	        include fastcgi_params;
	    }
	}
	
### 7.5 修改php-fpm的unix目录的用户及组

	chown nginx:nginx /var/run/php-fpm/ -R
	chown nginx:nginx /var/lib/php/ -R
	
### 7.6 重启所有服务

	systemctl restart radiusd.service 
	systemctl restart mysqld.service 
	systemctl restart nginx

重新启动php-fpm服务：

	systemctl restart php-fpm
	systemctl enable php-fpm


## 八 配置防火墙

配置防火墙，打开radius服务端口

	# (如果已经打开firewalld的话，可以跳过该步骤)
	# systemctl enable firewalld
	# systemctl start firewalld
	# systemctl status firewalld
	
添加radius服务

	cat /usr/lib/firewalld/services/radius.xml
	[root@bj-sg-johan-test ~]# firewall-cmd --list-services
	dhcpv6-client ssh
	[root@bj-sg-johan-test ~]# firewall-cmd --add-service=radius --permanent
	success
	[root@bj-sg-johan-test ~]# firewall-cmd --reload
	success
	[root@bj-sg-johan-test ~]# firewall-cmd --list-services
	dhcpv6-client ssh radius
	
	firewall-cmd --permanent --add-service=http 
	firewall-cmd --permanent --add-service=https
	
**设置8080 daloradius服务端口 不然连不上**

	firewall-cmd --permanent --add-port=8080/tcp
	firewall-cmd --permanent --add-port=8000/tcp
	firewall-cmd --reload
	
	
※ [特殊化定制]：如果不想让raidus端口暴漏在公网上，可以只允许特定IP地址访问radius服务

	firewall-cmd --permanent --new-zone=radius
	firewall-cmd --reload
	
	firewall-cmd --permanent --zone=radius --set-target=ACCEPT
	firewall-cmd --permanent --zone=radius --add-service= radius
	firewall-cmd --permanent --zone=radius --add-source=192.168.111.222/32
	firewall-cmd --permanent --zone=radius --add-source=192.168.111.223/32
	firewall-cmd --reload
	firewall-cmd --get-active-zones
	
## 九  登录daloradius

默认的管理员的用户名是administrator，密码是xxx。

![主界面](http://niubencoolboy.github.io/img/blogs/daloraidius_index.png)

### 9.1 修改管理员密码

在Config 菜单栏 进入Operators选项，在edit operator 输入administrator Enter 就可以修改啦。

![修改密码](http://niubencoolboy.github.io/img/blogs/daloraidius_passwd.png)

### 9.2 daloRadius 使用
参考博客：[https://yq.aliyun.com/articles/434274](https://yq.aliyun.com/articles/434274)

**参考：**

1. [http://www.racksam.com/2017/03/02/centos7-install-freeradius/](http://www.racksam.com/2017/03/02/centos7-install-freeradius/)
2. [http://www.racksam.com/2017/06/08/centos7-php7-nginx-mariadb-wordpress/](http://www.racksam.com/2017/06/08/centos7-php7-nginx-mariadb-wordpress/)
3. [https://tzclouds.com/2018/05/17/installation-freeradius-and-daloradius-on-centos-7-and-rhel-7/](https://tzclouds.com/2018/05/17/installation-freeradius-and-daloradius-on-centos-7-and-rhel-7/)


