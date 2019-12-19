Linux 环境, 假设这台机器名字叫dev(机器的hostname), 每次执行sudo 就出现这个警告讯息:  
`sudo: unable to resolve host dev`  
虽然sudo 还是可以正常执行, 但是警告讯息每次出来还是有点烦人, 而这只是机器在反解上的问题, 所以就直接从/etc/hosts 设定, 让dev(hostname) 可以解回127.0.0.1 的IP 即可.

/etc/hosts 原始内容

	 127.0.0.1       localhost
	
	# The following lines are desirable for IPv6 capable hosts 
	::1     localhost ip6-localhost ip6-loopback ip6-loopback
	fe00::0 ip6-localnet 
	ff00::0 ip6-mcastprefix 
	ff02::1 ip6-allnodes
	ff02::2 ip6-allrouters 
	ff02::3 ip6-allhosts

于127.0.0.1 localhost 后面加上主机名称(hostname) 即可, /etc/hosts 内容修改成如下:
	
	127.0.0.1       localhost dev
	# 或改成下面这两行
	#127.0.0.1      localhost
	#127.0.0.1       dev
	#或改成下面这两行(这种方式某些应用程序可能运行出现问题，如hadoop）
	#127.0.0.1 localhost
	#127.0.1.1dev

这样设完后, 使用sudo 就不会再有那个错误讯息啰~ :)

转载自：[sudo 出现unable to resolve host 解决方法](http://blog.csdn.net/yuzhiyuxia/article/details/19998665)