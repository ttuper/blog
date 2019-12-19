使用php的过程中，发现某个扩展没有添加，又不想重新编译php，这个时候我们就需要单独添加需要的扩展模块。  
下面以mysqli扩展模块为例，具体介绍安装步骤。

**1 安装mysql**  
具体参考：[Ubuntu下安装LNMP之Mysql的安装及卸载](https://www.jianshu.com/p/3933a387fccf)

**2 编译生成扩展**  
2.1 进入php源程序目录中的ext文件夹中，即下载的并解压后的php目录的ext中，ls后找到你需要添加的模块，比如mysqli模块：  
`cd mysqli`

2.2 然后执行phpize，生成编译文件，phpize在php安装目录的bin目录下，例如我的安装目录是：/usr/local/php，即：  
`/usr/local/php/bin/phpize`  
如果无误，则会提示如下内容：  

	Configuring for:
	PHP Api Version: 20170718
	Zend Module Api No: 20170718
	Zend Extension Api No: 320170718

可能出现的错误1：

	Cannot find config.m4. 
	Make sure that you run '/usr/local/php/bin/phpize' in the top level source directory of the module

这个错误的解决方法就是执行phpize时，你必须要cd到扩展目录中去，我们这里是mysqli，那么就不能在ext目录或别的目录，这样才能在mysqli的目录中生成编译文件，用于编译。

可能出现的错误2：

	Cannot find autoconf. Please check your autoconf installation and the
	$PHP_AUTOCONF environment variable. Then, rerun this script.

这时候就需要安装autoconf，只要执行`apt-get install autoconf`即可。

执行好phpize后，在mysqli中就会生成相应的编译文件configure。接下来，编译生成模块：

	./configure --with-mysqli=/usr/bin/mysql_config --with-php-config=/usr/local/php/bin/php-config

2.3 再然后，make  
这时候可能会出现这种错误：fatal error: ext/mysqlnd/mysql_float_to_double.h: 没有那个文件或目录

解决办法：这是因为当前是在mysqli这个目录下进行的编译，这个目录下是肯定没有`ext/mysqlnd/mysql_float_to_double.h`这个目录及文件的，所以，我们可以切换目录 `cd php-7.2.2`
在这个目录下重新编译即可

	./ext/mysqli/configure --with-mysqli=/usr/bin/mysql_config --with-php-config=/usr/local/php/bin/php-config

2.4 然后执行make 和make install，执行无误的话将提示：  

	Installing shared extensions: /usr/local/php/lib/php/extensions/no-debug-non-zts-20170718/
	Installing header files: /usr/local/php/include/php/

**3 修改配置**  
在php.ini中找到extension=mysqli.so，将其前面的分号去除，重启php-fpm即可。

综上，其他的扩展模块也是按照如此步骤，如果想要安装的扩展模块在源程序ext中不存在，可wget下载安装后再如上步骤添加。
