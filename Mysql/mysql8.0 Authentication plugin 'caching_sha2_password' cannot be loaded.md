mysql 8.0改变了身份验证插件，改成使用老版本的身份验证插件方式就好了。

	cd "C:\Program Files\MySQL\MySQL Server 8.0\bin"
	
	C:\Program Files\MySQL\MySQL Server 8.0\bin> mysql -u root -p
	Enter password: *********
	
	mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
	Query OK, 0 rows affected (0.10 sec)
	
	mysql> exit
