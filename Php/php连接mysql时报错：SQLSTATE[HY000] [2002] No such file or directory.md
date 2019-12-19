在Ubuntu 下安装了集成环境xampp， 在使用php连接mysql数据库，页面会出现 php连接mysql时报错：SQLSTATE[HY000] [2002] No such file or directory  这样的错误。

自己的解决方案如下

（1）控制台中敲入  ps aux | grep -i mysql 命令

找到mysqld.sock 的路径

	--socket=/var/run/mysqld/mysqld.sock

（2）然后通过sudo  find / -name php.ini

找到php.ini的位置

然后修改其中的的三个default_socket的值,使其等于mysqld.sock 的路径

	pdo_mysql.default_socket=/var/run/mysqld/mysqld.sock
	mysql.default_socket=/var/run/mysqld/mysqld.sock
	mysqli.default_socket=/var/run/mysqld/mysqld.sock


（3）重启服务器就好了。

原文：[php连接mysql时报错：SQLSTATE[HY000] [2002] No such file or directory](https://blog.csdn.net/worldmakewayfordream/article/details/40891001)
