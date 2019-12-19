当我按照之前文章中安装LNMP的步骤安装好环境后，打开phpinfo()却发现phpinfo中Loaded Configuration File 为 none，明明在预编译php时不仅指定了php的安装目录也指定了配置文件的目录，而且etc目录中的确存在php.ini文件。

那么，就可能是php并没有去找etc目录，而是去找了lib目录，解决方法有二：  
1. 将/usr/local/php/etc目录下的php.ini拷贝到/usr/local/php/lib目录下   
`cp /usr/local/php/etc /usr/local/php/lib/php.ini`  
2. php-fpm指定php.ini路径  
`/usr/local/php/sbin/php-fpm -y /etc/php-fpm.conf -c /etc/php.ini`
