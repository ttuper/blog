其实可以直接使用可视化工具，例如navite来进行导出，以下为使用mysql命令导出：

**方法一：**
查询语句直接输出
语法格式：

	Example：     
	select * into outfile '/data/var-3307/catid.xls' from help_cat where 1 order by cat_id desc limit 0,20;
	
	select * into outfile '导出文件存放目录' from 表面和查询条件等信息！

![结果](https://upload-images.jianshu.io/upload_images/9899281-9c59cea47b2235f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请注意权限问题，如果实在不行，放在/tmp 目录下

**方法二：**

在shell命令行下把Excel以文本方式打开，然后另存为，在编码选择ansi编码保存
语法格式

	Example：
	echo "select * from db_web.help_cat where 1 order by sort desc limit 0,20" | mysql -h127.0.0.1 -uroot > /data/sort.xls
	 
	echo 查询语句 管道 登录mysql链接方式 > 定向输出文件

![结果](https://upload-images.jianshu.io/upload_images/9899281-54f4b9ded8ad457d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**方式三：**

查询定向输出为Excel文件后缀，然后转码
语法格式：
	
	Example:
	    mysql db_web -uroot  -e "select * from help_cat where 1 order by type desc limit 0,20" > /data/type.xls
	 
	    mysql链接信息 数据库 用户名 密码 然后执行查询语句，定向输出。

![结果](https://upload-images.jianshu.io/upload_images/9899281-c553adbafdab9e44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将文件下载到本地，打开如果中文乱码，因为office默认的是gb2312编码，服务器端生成的很有可能是utf-8编码，这个时候你有两种选择:

1、在服务器端使用iconv来进行编码转换
`iconv -futf8 -tgb2312 -otype1.xls type.xls`
如果转换顺利，那么从server上下载下来就可以使用了。

2、转换如果不顺利，则会提示:
`iconv: illegal input ``sequence` `at` `position 1841`

类似错误，如下解决：

      先把type.xls下载下来，这个时候文件是utf-8编码的，用excel打开，乱码。把type.xls以文本方式打开，然后另存为，在编码选择ANSI编码保存。

原文地址：[https://www.cnblogs.com/qiaoyihang/p/6398673.html](https://www.cnblogs.com/qiaoyihang/p/6398673.html)

