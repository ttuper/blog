最近在看微信小程序，用到了以下这个方法：setStorageSync

![](https://upload-images.jianshu.io/upload_images/9899281-3e8dd4de324c28ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后，我的代码是这样的：

	var A = new Array() //也可以简写成 var A = []
	A['title'] = 1
	console.log(A)  
	wx.setStorageSync('test', A)
	console.log(wx.getStorageSync('test'))

很明显的，我定义了一个A数组，由此可见A是一个对象，如果不信，可以使用typeof A来查看下，然后我们给A数组中添加了一个键名为title的键值，最后我们调用setStorageSync来存入本地缓存。
按道理来说，文档上说的我们都符合了，data要求的是对象，我们的A就是对象，理想情况下是可以取出相同的值  

![console.log(A)的返回值](https://upload-images.jianshu.io/upload_images/9899281-9105f370c07311fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

但是，我们去出来的确实这样的

![console.log(wx.getStorageSync('test'))的返回值](https://upload-images.jianshu.io/upload_images/9899281-5d501cbc6e600d9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
这时候就纳闷了，我存进去了，但是我没取出来，或者说取出来的没有了title这个值。  
首先，看两个返回结果有什么相同之处，length都为0，一个正常索引数组会返回什么呢，我们试验下  

![索引数组返回的值](https://upload-images.jianshu.io/upload_images/9899281-c0b5b4bc2faf4785.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

可以看到这里的length=3，是有值的。

于是我就搜索了关于关联数组的相关资料，得知，在javascript中并没有“关联数组”一说，上面的代码演示所谓的"关联数组"，这种方式更为人性化，但是上面代码添加的并不是数组中的元素，实事上是为A对象添加的属性和属性值

其实在此之前，一直以为javascript中是有关联数组的，而且也一直用着，并没有去深究其中的异同之处。

这里可以把A“关联数组”转换为json对象，然后进行存储，就没有问题了，取出来也可以转换为数组形式。具体原因后续在补充了

