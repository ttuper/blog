##### 问题出现背景
使用thinkphp5.0搭建了一个后台管理系统，访问页面时发现页面加载时间超级慢，一个普通的显示文本没有使用到数据库的页面都需要1秒多，十分影响体验（虽然后台管理系统慢点也没啥大影响哈，但是出问题了不解决心里十分不爽）

##### 运行环境
操作系统：win7
php版本：5.6.31
nginx版本：1.12.2
apache版本：2.4
mysql版本：5.6.37
mvc框架：thinkphp5.0

##### 解决思路
出现问题后，一开始以为是thinkphp5框架渲染页面的时候慢，所以在网上搜索的时候搜索的关键词就是“thinkphp5加载页面慢”，网上的说法有很多，最多的是以下几种：

1、你把连接数据库的host从localhost更改成127.0.0.1，给出的解释为：

1）PHP5.3以上，如果是链接localhost，会检测是IPV4还是IPV6，所以会比较慢。

2）windows系统下localhost是先进行本地HOST解析，然后走TCP/IP协议进行连接，127.0.0.1直接使用TCP/IP协议进行连接。[这里可以参考下这篇文章 [PHP连接本地数据库用localhost还是127.0.0.1？](http://www.devkang.com/how-to-connect-local-mysql-notes)]

以上两种解释我都没有一一验证，感兴趣的朋友可以自己验证下。这里我选择都采纳，跟别人解释的话我就这样解释，哈哈

2、thinkphp5本身不慢，这也能怪到框架上去，也是醉了（这个答案就忽略不计）

主要就是上面两种说法，一般情况下，如果你连接数据库时使用的host是localhost的话，跟换成127.0.0.1的确会快不少，然而，我的本身就是127.0.0.1了，所以忽略。

后来我就将项目部署到阿里云服务器上，php环境和本地几乎是一毛一样的，然后发现，线上运行速度超级快，本地是秒级的，而线上是毫秒级的，相差很大，切换页面基本没什么感觉。这时候就尴尬了，于是我把关键词从“thinkphp5加载页面慢”改成“php本地执行慢”，下面是我遇到的大部分答案：

1、将apache或者nginx的日志文件清理下，太大了会影响运行速度。（我的本地日志本身就很少，pass）

2、将host文件中的#127.0.0.1 localhost前面的#号去掉。（已试，速度是稍微快了那么一丢丢，但是没什么根本解决倾向，采用）

3、将localhost改成127.0.0.1（这个答案同上，数据库连接相关了，pass）

4、直接使用127.0.0.1来访问（我试过了，本来我是用nginx来进行转发的，特地关了nginx，更改apache的httpd.conf，返现运行速度没有太大变化，pass）

查到这里的时候，所有的方案都试过了，都没有解决，我都快放弃了......

换关键词再搜索呗，“php访问本地TTFB时间长”，继续搜，不放弃。

然后找到了，[PHP网站（Drupal7）响应过慢之“Wating(TTFB)时间过长”](https://www.cnblogs.com/zjfblog/p/8202465.html)

这篇文章中，就提到了另一种思路，关闭xdebug，然后我想起来我的php.ini中的确开启了xdebug，反正现在没有别的招了，死马当活马医呗。然后，当当当当，解决啦。对比下时间差：
![xdebug关闭前有数据库访问时响应时间](https://upload-images.jianshu.io/upload_images/9899281-40ac71fa14ea4fa7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![xdebug关闭后有数据库访问时响应时间](https://upload-images.jianshu.io/upload_images/9899281-cd9fc8ce6f6c4066.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

两张图的对比十分明显，时间差根本就不是一个数量级的了，体验也嗖嗖的，哈哈。

一开始我还纳闷，阿里云服务器上也开启了xdebug呀，不也一样超级快，后来想人家毕竟是专业的服务器，肯定性能也不是一个级别的了，这个就不纠结了。

关于xdebug，大家可以自行百度，我这边参考的文章是 [php安装xdebug调试PHP程序的很慢问题解决办法](https://yq.aliyun.com/ziliao/48526)

因为你知道是什么原因导致慢了，关键词也就定位到了，接下来可以搜索xdebug，xdebg，xdebug......哈哈
