**背景：**  
最近在千龙网进行技术交接，需要打包他们服务器上的所有图片，这个量是非常大的，几乎每个日期文件打包下来差不多都要两三百个G，一开始我是直接使用tar命令直接打包的，我觉得也没问题，但是，打包一段时间后，xshell软件就在命令行中报错了，说socket error，打包进程也就断了。  
这时候千龙网的技术就告诉我，你可以使用screen命令来开一个虚拟窗口，这样，就不会出现这个问题，也不会影响你的其他操作。其实之前都没听过这个命令，好激动，又要学会一个知识点了。

**命令介绍：**  
[screen](http://linux.51yip.com/search/screen)

**使用方法：(此处以我这次的压缩为例)**  
1、打开一个虚拟窗口

	// 2014为虚拟窗口的名称，可根据实际情况命名
	[root@linuxidc www.linuxidc.com] screen -S 2014 

2、在窗口中执行压缩  

	[root@linuxidc www.linuxidc.com] tar zcvf ./2014.tzr.gz ./2014

3、暂时离开当前session，将目前的 screen session (可能含有多个 windows) 丢到后台执行，并会回到还没进 screen 时的状态  
`ctrl + a + d`  
4、查看当前有哪些虚拟窗口  

	[root@linuxidc www.linuxidc.com] screen -ls

5、进入刚才的2014虚拟窗口(恢复离线的screen进程，如果有多个断开的进程，需要指定[pid.tty.host])  

	[root@linuxidc www.linuxidc.com] screen -r 2014


以上仅是部分screen命令的操作，具体细节及其他可参考上述命令介绍