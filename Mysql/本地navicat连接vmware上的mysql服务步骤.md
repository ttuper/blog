默认情况下，mysql只允许本地登录，如果要开启远程连接，则需要修改/etc/mysql/my.conf文件（我修改的是/etc/mysql/mysql.conf.d/mysqld.cnf）。

一、修改/etc/mysql/my.conf

找到bind-address = 127.0.0.1这一行
改为bind-address = 0.0.0.0即可
127.0.0.1是只支持本地连接的，故要改成0.0.0.0

二、为需要远程登录的用户赋予权限

1、新建用户远程连接mysql数据库

	grant all on *.* to admin@'%' identified by '123456' with grant option; 
	flush privileges;

允许任何ip地址(%表示允许任何ip地址)的电脑用admin帐户和密码(123456)来访问这个mysql server。
注意admin账户不一定要存在。

2、支持root用户允许远程连接mysql数据库

	grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
	flush privileges;

三、查看系统用户

	use mysql;
	select user,host from user;

原文： [配置mysql允许远程连接的方法](https://www.cnblogs.com/linjiqin/p/5270938.html)