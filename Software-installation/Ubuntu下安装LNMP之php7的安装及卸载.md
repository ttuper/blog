据了解，php7是比之前的版本性能快很多的。http://php.net/get/php-7.2.2.tar.gz/from/a/mirror  
安装前也可提前将相关依赖库安装好，或者在安装php时若安装失败，按照提示安装好所需要的依赖库即可。

# 1 安装Php

### 1.1 apt-get 一键安装

	sudo apt-get update
	sudo apt-get install php

### 1.2 手动编译安装
	
	# 下载最新版本 |
	wget http://php.net/distributions/php-7.2.2.tar.gz 
	# 解压 
	tar -zxvf php-7.2.2.tar.gz
	# 进入解压目录 
	cd php-7.2.2
	# 预编译 
	sudo ./configure --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --enable-fpm 
	## 这里是裸装模式，仅指定了安装目录，配置文件所在目录及打开fpm，
	## 想要一次性安装所需模块，可参考./configure --help，或者参考文章“Ubuntu下安装LNMP之独立添加php扩展模块”
	# 编译php 
	sudo make 
	#安装php 
	sudo make install 
	# 修改配置文件 
	cd /usr/local/php/etc 
	sudo cp php-fpm.conf.default php-fpm.conf 
	cd /usr/local/php/etc/php-fpm.d 
	sudo cp www.conf.default www.conf 
	# 修改www.conf中的所属用户组及用户 
	user=www 
	group=www 
	## 注意： 如果www用户不存在，则先添加用户www用户 
	sudo groupadd www 
	sudo useradd -g www www 
	# 查看php-fpm进程 
	ps -ef|grep php-fpm 
	# 配置php-fpm开机自启动 
	sudo /usr/sbin/chkconfig php-fpm on

注意：以上sudo可加可不加，仅在权限不足时临时获得根用户权限。可查看[Ubuntu下安装LNMP之nginx的安装](http://www.cnblogs.com/totian/p/8423739.html)  
当使用chkconfig时，ubuntu会提示“找不到该命令”，此时可参考[Ubuntu 14.04 安装 chkconfig 服务管理程序](https://www.linuxidc.com/Linux/2017-10/147518.htm)

# 2 php-fpm常用命令

### 2.1 启动php-fpm
	
	sudo /usr/local/php/sbin/php-fpm 
	# 注意：若是已经将php-fpm加入系统服务，可不用输入那么长的地址，具体步骤后续讨论，和shell相关。 

### 2.2 停止php-fpm

`sudo pkill php-fpm `

### 2.3 查看Php版本

	sudo /usr/local/php/sbin/php -v 
	PHP 7.2.2 (cli) (built: Feb 22 2018 15:43:13) ( NTS )
	Copyright (c) 1997-2018 The PHP Group
	Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies


# 3 卸载php

卸载其实相当容易，只要记住在Linux中，一切皆文件即可。所以，我们可以像在windows上删除快捷方式那样直接找到安装php的目录将其删除即可。例如，我安装的目录是/usr/local/php，只要将该目录删除，并kill掉当前运行的php-fpm进程即可。

# 4 安装过程中遇到的问题

.configure进行编译时，提示 error: xml2-config not found. Please check your libxml2 installation.  
这是由于系统缺少PHP所需的库文件导致的，根据提示，进行如下操作：　  
`sudo apt-get install libxml2`（按Tab键）  
将会看到如下信息：  
`libxml2 libxml2-dev libxml2-headers libxml2-utils`  
这里需要安装的是libxml2-dev软件包，操作如下：  
 `sudo apt-get install libxml2-dev`  
库文件安装完成后，重新预编译PHP

相关说明：  
fmp的配置文件是xml格式的，用了libxml对配置文件进行解析，所以需要安装libxml2的开发文件；
一般这种命名规则: lib, lib-dev, 前一个是程序运行时需要的库, 后一个是编译源码时需要的库.
