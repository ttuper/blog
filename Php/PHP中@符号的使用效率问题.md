在观看[性能优化之PHP优化](https://www.imooc.com/video/4179)时，讲到产生额外开销的错误抑制符@

![视频截图](https://upload-images.jianshu.io/upload_images/9899281-e2ca21451b461fb1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看了视频中老师的演示后，的确发现使用@符号后，代码开始前和结束后会增加额为的Opcode。这回影响代码执行效率。

在网上查找相关资料时，也有ITer指出如下栗子：

使用@时的实际操作为：

	保存当前的error_reporting值, 并设置error_reporting(0); //关闭错误输出
	恢复之前保存的error_reporting值.


@include('file'); 
表示为代码即

	$foo = error_reporting(0);
	include('file');
	error_reporting($foo);

因此如果我们需要大量的使用@ 
不如手动的控制 error_reporting 以节省多次重复动作的开销
