#### 问题背景
项目中要进行缩略图的裁剪，产生如下错误Call to undefined function imagecreate()
该错误的产生其实是php中gd库扩展没有安装的原因

#### 安装gd库
因为我之前安装php7.1时下载解压的安装包还在，所以我直接使用[Ubuntu下安装LNMP之独立添加php扩展模块](https://www.jianshu.com/p/afec44b47682)该文中的方法来安装

	$ cd php-7.1.7/ext/gd
	$ /usr/local/php/bin/phpize
	$ ./configure --with-php-config=/usr/local/php/bin/php-config

如上所述，会出现如下错误：  
checking for jpeg_read_header in -ljpeg... yes  
configure: error: png.h not found.

直接查看这篇文章[PHP7源码编译安装](https://my.oschina.net/bibyzhang/blog/541015)