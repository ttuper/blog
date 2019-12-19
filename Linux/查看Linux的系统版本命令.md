**1、查看Linux系统内核版本命令**  
**方法一**  
`cat /proc/version`  
返回结果：  

	Linux version 2.6.32-431.el6.x86_64 (mockbuild@c6b8.bsys.dev.centos.org) 
	(gcc version 4.4.7 20120313 (Red Hat 4.4.7-4) 
	(GCC) ) #1 SMP Fri Nov 22 03:15:09 UTC 2013

**方法二**  
`uname -a`  
返回结果：  

	Linux LMTSERVER1 2.6.32-431.el6.x86_64 #1 
	SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux


**2、查看Linux系统版本的命令**  
**方法一**  
`lsb_release -a`  
返回结果：  

	No LSB modules are available.
	Distributor ID:	Ubuntu
	Description:	Ubuntu 18.04.2 LTS
	Release:	18.04
	Codename:	bionic

该方法适用于所有的Linux发行版，包括RedHat、SUSE、Debian…等发行版（此处发行版个人理解是有图形化界面的，可以有办公软件安装的）。 【一个典型的Linux发行版包括：Linux内核，一些GNU程序库和工具，命令行shell，图形界面的X Window系统和相应的桌面环境，如KDE或GNOME，并包含数千种从办公套件，编译器，文本编辑器到科学工具的应用软件。（摘自百度百科）】  
**方法二：**  
`cat /etc/issue`  
返回结果：  

	Ubuntu 18.04.2 LTS \n \l

**方法三：**  
`cat /etc/redhat-release`  
返回结果：  

	CentOS release 6.5 (Final)

该方法适用于这种方法只适合Redhat系的Linux

同时可以参考[Linux发行版：CentOS、Ubuntu、RedHat、Android、Tizen、MeeGo](https://blog.csdn.net/ithomer/article/details/9729933)  
这篇文章介绍了发行版和社区版还有各系列的区别