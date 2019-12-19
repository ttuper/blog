最近在看thinkphp5的视频内容，学习到了在thinkphp中如何自定义异常输出，而不是在遇到异常或错误后让框架本身渲染出一个错误页面。  
然后我就想到之前一直使用的Yii框架，目前使用的是Yii2.0+版本。  
在开发过程中，只要一遇到未定义的变量或者查询数据库时查询出错，Yii就会向用户抛出一个这样的页面：  
![更改之前](https://upload-images.jianshu.io/upload_images/9899281-fd48b9f891589afb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
页面内容很丰富，从中我们可以知道错误的文件，错误的行数，错误的提示信息......  
但是，这样的页面客户端是解析不了的，出现这个页面会导致客户端崩溃，就是所谓的卡死。  
那肿么办呢？  
我尝试了在官方手册上找答案，但是却一无所获，也可能是我没找到点子上。根据thinkphp5的经验，是该配置文件中的一项，然后重写框架中的渲染错误页面方法即可。  
于是，我按照上面的思路这样做的，从框架入口文件开始找：  
![入口文件](https://upload-images.jianshu.io/upload_images/9899281-7ca014ddcbeb61a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
找到yii\web\Application这个类中的构造函数__construct  
![构造函数](https://upload-images.jianshu.io/upload_images/9899281-11487e7dcba4e4b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
已经看到注册错误处理的地方了，继续：  
![找到了](https://upload-images.jianshu.io/upload_images/9899281-89af816cfb3418b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
看到了吗，这段代码说了，如果$config['components']['errorHandler']['class']没有设置，系统就会报错，但是该值是有默认值的，默认的就是“yii\web\ErrorHandler”  
到这里应该就很明晰了，我们再进入“yii\web\ErrorHandler”来查看其中渲染异常页面的方法：  
![最终](https://upload-images.jianshu.io/upload_images/9899281-9a84109f99ae35ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
好了，接下来我们来重写这个方法吧。  
我是重新建了一个文件夹叫做components，然后新建了一个叫Exception.php的文件。其中代码如下：

	<?php
	namespace app\components;
	
	use yii\web\ErrorHandler;
	
	class Exception extends ErrorHandler {
	    
	    /**
	     * 重写渲染异常页面方法
	     * @param type $exception
	     */
	    public function renderException($exception) {
	        $data = [
	            'code' => 500,
	            'msg' => $exception->getMessage(),
	            'data' => [
	                'file' => $exception->getFile(),
	                'line' => $exception->getLine()
	            ]
	        ];
	        echo json_encode($data);
	        die;
	    }
	}

同时在配置文件中，即config/web.php中的component中的errorHandle中增加class项：

	'errorHandler' => [
	            'errorAction' => 'site/error',
	            'class' => 'app\components\Exception' //此处新增
	        ],

如此这般即可，验证下吧，出现异常后，返回的就不是页面内容了，而是json串：  
![更改后](https://upload-images.jianshu.io/upload_images/9899281-f94db4045a9865ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



