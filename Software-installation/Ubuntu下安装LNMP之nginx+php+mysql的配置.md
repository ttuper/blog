安装之前的步骤依次安装好Nginx、php、mysql后，接下来我们讲一下如何进行相关配置，使之可以正常运转。
## 1 Nginx的配置
在nginx的配置文件nginx.conf中找到如下语句：

	location / {
	　　root html;
	　　index index.html index.htm;
	}

在index.html后添加上index.php，使nginx可以正常捕捉到index.php文件。修改后则为：

	location / {
	　　root html;
	　　index index.html index.htm index.php;
	}

然后再找到如下内容：

	#location ~ \.php$ {
	#　　root html;
	#　　fastcgi_pass 127.0.0.1:9000;
	#　　fastcgi_index index.php;
	#　　fastcgi_param SCRIPT_FILENAME /scripts$fastcgi_script_name; 
	#　　include fastcgi_params;
	#}

将其前面#删除，即解除注释，并将/scripts更改为$document_root，$document_root 代表当前请求在root指令中指定的值。也可将root目录更改到你想要的目录。  
重新启动nginx服务

## 2 php的配置
2.1 按照[Ubuntu下安装LNMP之php7的安装及卸载](https://www.jianshu.com/p/1ff101482b31)安装好的php安装目录中是没有php.ini文件的，此时需要我们从源文件中的php.ini.development拷贝一份放到原先预编译时指定的`--with-config-file-path`目录中，并更名为php.ini。


2.2 修改php-fpm配置文件(若之前做过该步骤可忽略)

	cd /usr/local/php/etc 
	sudo cp php-fpm.conf.default php-fpm.conf 
	cd /usr/local/php/etc/php-fpm.d 
	sudo cp www.conf.default www.conf
	# 修改www.conf中的所属用户组及用户 
	user=www 
	group=www 
	## 注意： 如果www用户不存在，则先添加用户www用户 
	sudo groupadd www 
	sudo useradd -g www www 

重启php-fpm

## 3 Mysql的配置
在php中，mysql是作为一个模块整合到php中的，要想php能够正常使用mysql，只要在安装php过程中预编译时加上配置或者参考[Ubuntu下安装LNMP之独立添加php扩展模块](https://www.jianshu.com/p/afec44b47682)进行独立安装即可。

在工作目录中新建index.php，写上：

	<?php
	phpinfo();

查看是否成功访问并且mysql模块是否加载成功。  
最后，每次更改配置后，切记重启nginx及php-fpm服务。

