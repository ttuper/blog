Nginx 最初是作为一个 Web 服务器创建的，用于解决 C10k 的问题。作为一个 Web 服务器，它可以以惊人的速度为您的数据服务。但 Nginx 不仅仅是一个 Web 服务器，你还可以将其用作反向代理，与较慢的上游服务器（如：Unicorn 或 Puma）轻松集成。你可以适当地分配流量（负载均衡器）、流媒体、动态调整图像大小、缓存内容等等。  
基本的 nginx 体系结构由 master 进程和其 worker 进程组成。master 读取配置文件，并维护 worker 进程，而 worker 则会对请求进行实际处理。  
正式开始前，编译环境gcc g++ 开发库之类的需要提前装好。  
一般我们都需要先装pcre, zlib，前者为了重写rewrite，后者为了gzip压缩。

# 1 安装Nginx依赖库

###1.1 安装gcc g++依赖库

	sudo apt-get install build-essential
	sudo apt-get install libtool

sudo 的意思是赋予当前登录用户某某权限（super user do），也可以直接apt-get install ...，如果直接使用apt-get提示权限不足时，可以加上sudo来操作，就OK了。可以参考：[ubuntu使用su切换root用户提示“认证失败”](http://www.cnblogs.com/totian/p/8422148.html)来了解sudo的作用。如果当前你登录的是root根用户，直接使用apt-get即可。接下来的操作如果涉及到权限不足，皆可使用sudo或者切换到根用户下。  
（gcc和g++区别和作用参考：[http://www.cnblogs.com/samewang/p/4774180.html](http://www.cnblogs.com/samewang/p/4774180.html)）

### 1.2 安装pcre依赖库

	sudo apt-get update 
	sudo apt-get install libpcre3 libpcre3-dev 

apt-get，是一条linux命令，适用于deb包管理式的操作系统，主要用于自动从互联网的软件仓库中搜索、安装、升级、卸载软件或操作系统。  
apt-get update 的意思是更新当前系统软件包列表。具体可参考该命令详解。  
nginx的rewrite伪静态匹配规则用到正则，pcre就用来干这个。具体查看[360百科](https://baike.so.com/doc/5497195-5735108.html)

### 1.3 安装zlib依赖库

`sudo apt-get install zlib1g-dev` 

### 1.4 安装ssl依赖库

`sudo apt-get install	 openssl` 

OpenSSL是一个开放源代码的SSL协议的产品实现，它采用C语言作为开发语言，具备了跨系统的性能。调用OpenSSL的函数就可以实现一个SSL加密的安全数据传输通道，从而保护客户端和服务器之间数据的安全。

一路走下来，我们可以发现，所有的依赖库啊之类的基本上都是用C或者C++来编写的，然后通过gcc或者g++来编译，可见C和C++的强大。

# 2 安装Nginx

### 2.1 apt-get 一键安装

	sudo apt-get update 
	sudo apt-get install nginx 

### 2.2 手动编译安装

	# 下载最新版本 
	wget http://nginx.org/download/nginx-1.2.2.tar.gz
	# 解压
	tar -zxvf nginx-1.2.2.tar.gz
	# 进入解压目录
	cd nginx-1.2.2
	# 预编译nginx
	sudo ./configure --prefix=/usr/local/nginx
	## --prefix=dir 的意思是指定nginx安装目录，可以用./configure --help查看相关配置信息
	# 编译nginx
	sudo make
	#安装nginx
	sudo make install
	# 启动nginx
	sudo /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
	## 注意：-c 指定配置文件路径，不加的话，nginx会自动加载默认路径的配置文件，可以通过 -h查看帮助命令。
	# 查看nginx进程
	ps -ef|grep nginx

注意：以上sudo可加可不加，仅在权限不足时临时获得根用户权限。可参考“1.1 安装gcc g++依赖库”中的介绍。

# 3 Nginx常用命令

### 3.1 启动Nginx

	sudo /usr/local/nginx/sbin/nginx
	# 注意：若是已经将nginx加入系统服务，可不用输入那么长的地址，具体步骤后续讨论，和shell相关。

### 3.2 停止Nginx

	sudo /usr/local/nginx/sbin/nginx -s stop
	# 注意：stop 是快速关闭，不管有没有正在处理的请求。
	sudo /usr/local/nginx/sbin/nginx -s quit
	# 注意：quit 是一个优雅的关闭方式，Nginx在退出前完成已经接受的连接请求。

-s 的意思是向nginx发送信号，signal的意思。可参考：[CommandLine](http://wiki.nginx.org/CommandLine)

### 3.3 重新加载配置信息

`sudo /usr/local/nginx/sbin/nginx -s reload`

### 3.4 指定配置文件

`sudo /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf`

-c 的意思是指定配置文件，configuration

### 3.5 查看Nginx版本

	sudo /usr/local/nginx/sbin/nginx -v
	# nginx version: nginx/1.2.2

要想查看具体Nginx版本信息，将小些的v改成大写的V即可。

将显示：

	nginx version: nginx/1.2.2
	built by gcc 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.4) 
	configure arguments: --prefix=/usr/local/nginx

### 3.6 检查Nginx配置文件是否有误

	sudo /usr/local/nginx/sbin/nginx -t
	# nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
	# nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful

出现上述提示就表示配置文件是正确的，否则将会有相关提示。

# 4 卸载Nginx
我在安装Nginx时，是采用自己从网上down下自己需要的nginx版本进行编译安装的，如果使用过apt库来进行安装的话可以参考这篇文章：[ubuntu中彻底删除nginx](http://www.cnblogs.com/Liqiongyu/p/5879012.html)  
假如是编译安装的童鞋，可以按照如下的步骤来进行卸载：  
编译时的路径如果指定了`--prefix /usr/local/xxx` 直接`rm -rf /usr/local/xxx`即可**(在linux系统中，一切即文件)**。  
如果安装时没有指定安装目录就到 `/usr/bin` `/etc` `/usr/sbin` `/usr/lib`找到相关文件手动删除

