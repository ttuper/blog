LNMP安装好后，写了个index.php文件，里面的内容很简单，就是想测试php与mysql的通信是否正常，代码如下：


	<?php
	$host = 'localhost';
	$user = 'root';
	$pass = '';
	$conn = mysqli_connect($host, $user, $pass);
	print_r($conn);

然后，就报错：  
`ERROR 1698 (28000): Access denied for user 'root'@'localhost'`  
查找网上的所有方法，几乎都试了个遍，其中包括，修改root用户密码，给root所有权限等等，不过我没有尝试重新创建新用户。

以上方法尝试后都不行。于是，接着搜索问题，碰运气看看有没有人遇到并解决之。

中文的搜不到，于是尝试看看英文，于是看到下面这篇：[https://stackoverflow.com/questions/39281594/error-1698-28000-access-denied-for-user-rootlocalhost](https://stackoverflow.com/questions/39281594/error-1698-28000-access-denied-for-user-rootlocalhost)

![截图](http://upload-images.jianshu.io/upload_images/9899281-f5e79ef625b1eeb9..png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Option 1:**
	
	$ sudo mysql -u root # I had to use "sudo" since is new installation
	
	mysql> USE mysql;
	mysql> UPDATE user SET plugin='mysql_native_password' WHERE User='root';
	mysql> FLUSH PRIVILEGES;
	mysql> exit;
	
	$ service mysql restart

**Option 2: (replace YOUR_SYSTEM_USER with the username you have)**

	$ sudo mysql -u root # I had to use "sudo" since is new installation
	
	mysql> USE mysql;
	mysql> CREATE USER 'YOUR_SYSTEM_USER'@'localhost' IDENTIFIED BY '';
	mysql> GRANT ALL PRIVILEGES ON *.* TO 'YOUR_SYSTEM_USER'@'localhost';
	mysql> UPDATE user SET plugin='auth_socket' WHERE User='YOUR_SYSTEM_USER';
	mysql> FLUSH PRIVILEGES;
	mysql> exit;
	
	$ service mysql restart

由此可见，原因是最近的Ubuntu安装（也许还有其他的），mysql默认使用UNIX auth_socket插件。

有兴趣的可以进一步做了解。
