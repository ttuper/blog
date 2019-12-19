php开发环境安装好后，这时候就可以进行初步编码了，当然，测试php连接mysql没有问题后我们就该正式建库建表玩玩了。  
当然，你也可以敲命令来建库建表，但这未免效率不太高，这时候我们可以使用图形化工具：navicate。  
当使用navicate进行远程连接mysql时，提示错误：  2003: Can't connect to MySQL server on '' (10038)

我的服务器是阿里云的服务器，mysql是5.7版本。

首先，我按照网上总结的解决方案走了一遍。

**步骤一：为mysql添加远程权限**  
具体可以参考：[2003 - Can't connect to MySQL server on 'x.x.x.x' (10038)](http://blog.csdn.net/a19881029/article/details/50805562)  
如果错误排除，那就不需要第二步了，否则继续往下看。

**步骤二：设置防火墙，允许3306端口**

	root@xxx:~# systemctl status firewalld.service
	● firewalld.service
	   Loaded: not-found (Reason: No such file or directory)
	   Active: inactive (dead)

发现防火墙是dead（阿里云ubuntu服务器默认是不开启防火墙的，当然，为了服务器的安全，建议开启），根本就没开启，那么可以排除这个原因了，如果开启了，可以设置开启3306端口的监听。  
具体参考：[阿里云 Ubuntu 16 配置iptables防火墙](http://blog.csdn.net/zhangyingchengqi/article/details/70808373)  
至此，配置了防火墙如果还不行，那就再继续吧。>>>>>>>

**那么，如果阿里云服务器防火墙根本就没开启，那又是什么原因导致无法远程连接mysql呢？**  
原来是阿里云官方的限制，关闭了3306端口。  
1.登录阿里云服务器，找到自己的实例，点击“管理”  
![1](http://upload-images.jianshu.io/upload_images/9899281-7e0645307e46569c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2.点击“本实例安全组”  
![2](http://upload-images.jianshu.io/upload_images/9899281-cc812c21cbe16c90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3.进入“配置规则”  
![3](http://upload-images.jianshu.io/upload_images/9899281-7454d887242ff2af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4.“快速创建规则”  
![4](http://upload-images.jianshu.io/upload_images/9899281-3b05ffd11178c69b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
5.选择mysql  
![5](http://upload-images.jianshu.io/upload_images/9899281-17b3daf51c7f94ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
6.这时候，mysql的安全规则就加入进去了  
![6](http://upload-images.jianshu.io/upload_images/9899281-dfb72d4a8bbae6d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时，再次远程连接，就已经可以了。





