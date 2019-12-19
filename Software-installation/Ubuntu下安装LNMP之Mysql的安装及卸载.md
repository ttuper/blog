Mysql安装包下载地址：[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)  
选择Ubuntu Linux + Ubuntu Linux 16.04（x86,64bit）后，我们可以看到会出现很多的安装包，有tar结尾的，也有deb结尾的，这里我们选择tar结尾的即可，下载下来解压之后里面包含了全部的deb单个文件，不用再一个一个下载了。

具体安装也可以参考官网安装文档：[https://dev.mysql.com/doc/refman/5.7/en/linux-installation-debian.html](https://dev.mysql.com/doc/refman/5.7/en/linux-installation-debian.html)  
以下，我仅对官网文档进行翻译及相应解说，不当之处，尽请谅解与指出。
# 1. 在Linux上使用由Oracle提供的Debian包来安装Mysql

Oracle提供了Debian包来在Debian系统或类Debian系统上安装mysql，这些安装包可以通过两个不同的渠道获得：  
渠道1： MySQL APT存储库。这是在Debian系统上安装MySQL的首选方法，因为它提供了一种简单方便的安装和更新MySQL的产品。有关详细信息，请参见[“安装MySQL在Linux上使用MySQL apt库”](https://dev.mysql.com/doc/refman/5.7/en/linux-installation-apt-repo.html)。  
渠道2： Mysql开发区的下载区域。详细信息可以参考[“如何获取Mysql”](https://dev.mysql.com/doc/refman/5.7/en/getting-mysql.html)。以下是一些可用的Debian安装包信息和安装指示信息：

###### 1.1 各种各样的Debian软件包在MySQL开发区提供了安装不同的组件在不同的Debian和Ubuntu平台的MySQL。
首选的方法是使用压缩包，其中包含MySQL的基本安装所需要的软件包。压缩包中mysql-server_mver-dver_cpu.deb-bundle.tar格式名称。mver是MySQL版本和对的Linux发行版本。CPU值表示创建包的处理器类型或家庭，如下表所示：

**Table MySQL Debian and Ubuntu Installation Packages CPU Identifiers**

| `CPU` Value | Intended Processor Type or Family |
| --- | --- |
| `i386` | Pentium processor or better, 32 bit |
| `amd64` | 64-bit x86 processor |

###### 1.2 下载后解压压缩包，用下面的命令：  
`shell> tar -xvf mysql-server_MVER-DVER_CPU.deb-bundle.tar `  
###### 1.3 你可能需要安装libaio1库如果系统中预先没有的话：  
`shell> sudo apt-get install libaio1`  
###### 1.4 使用下列命令来已配置mysql服务包（即设置登录密码）：  
`shell> sudo dpkg-preconfigure mysql-community-server_*.deb`  
您将被要求为MySQL安装提供root用户的密码。你也可能会被问到关于安装的其他问题。

注意：请记住您设置的root密码。稍后希望设置密码的用户可以在对话框中将密码字段留空，然后按OK；在这种情况下，使用MySQL套接字对等凭证身份验证插件对服务器的根访问进行身份验证，以使用UNIX套接字文件进行连接。你可以晚些时候使用mysql_secure_installation设置root密码。

###### 1.5 对于MySQL服务器的基本安装，安装数据库common文件包，客户端元软件包，client软件包，服务器包，和服务器元软件包（按顺序）；你也可以用一个命令：

	shell> sudo dpkg -i mysql-{common,community-client,client,community-server,server}_*.deb

###### 1.6 如果你被警告未满足依赖关系通过dpkg，你可以通过apt-get来修复：
`sudo apt-get -f install`

# 2 文件安装位置

这里是文件包安装位置：  
所有得配置文件，例如my.cf，都在/etc/mysql中；  
所有的二进制文件、库、头文件，等等，都是/usr/bin和/usr/sbin下；  
数据目录在/var/lib/mysql  
基本信息：  
-  /usr/bin                 客户端程序和脚本    
-  /usr/sbin                mysqld 服务器    
-  /var/lib/mysql           日志文件，数据库  ［重点要知道这个］    
-  /usr/share/doc/packages  文档    
-  /usr/include/mysql       包含( 头) 文件    
-  /usr/lib/mysql           库    
-  /usr/share/mysql         错误消息和字符集文件    
-  /usr/share/sql-bench     基准程序    

# 3 Mysql的卸载

卸载的时候也很方便，只要按照2.5中的倒序依次使用如下命令即可：

	sudo dpkg -P mysql-server 
	sudo dpkg -P mysql-community-server
	sudo dpkg -P mysql-client
	sudo dpkg -P mysql-community-client 
	sudo dpkg -P mysql-common

dpkg的相关命令可参考：[dpkg命令](http://man.linuxde.net/dpkg)  
Mysql的安装过程也可参考：[http://blog.csdn.net/qq_20565303/article/details/69813868](http://blog.csdn.net/qq_20565303/article/details/69813868)
