[Redis中文官网](http://www.redis.cn/download.html)，使用wget下载安装包  

	wget http://download.redis.io/releases/redis-4.0.8.tar.gz

解压该安装包，会在当前目录生成redis-4.0.8文件夹		

	tar -zxvf redis-4.0.8.tar.gz

切换到redis-4.0.8目录下，执行make和make install

	make
	make install

执行好后，/usr/local/bin目录下会生成以下文件

	-rwxr-xr-x  1 root root 2701008 Feb 28 18:13 redis-benchmark*
	-rwxr-xr-x  1 root root 6179224 Feb 28 18:13 redis-check-aof*
	-rwxr-xr-x  1 root root 6179224 Feb 28 18:13 redis-check-rdb*
	-rwxr-xr-x  1 root root 2894168 Feb 28 18:13 redis-cli*
	lrwxrwxrwx  1 root root      12 Feb 28 18:13 redis-sentinel -> redis-server*
	-rwxr-xr-x  1 root root 6179224 Feb 28 18:13 redis-server*

更改配置，让redis服务在后台运行

	mkdir /etc/redis
	cp redis-4.0.8/redis.conf /etc/redis/redis.conf
	vi /etc/redis/redis.conf
	# 打开redis.conf后修改daemonize为yes，代表redis服务后台运行

执行redis-server启动redis服务

	redis-server /etc/redis/redis.conf  # 启动redis服务
	redis-cli # 启动redis客户端
	127.0.0.1:6379> set key 111
	OK
	127.0.0.1:6379> get key
	"111"
	127.0.0.1:6379> del key
	(integer) 1
	127.0.0.1:6379> exit;

至此，redis安装成功。
