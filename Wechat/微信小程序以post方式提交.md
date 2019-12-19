最近在做微信小程序相关的后台开发，前端请求方式我同意定义成了post，然后前端开发小伙伴在微信小程序中将get改成post后居然无法请求成功，这我就纳闷了，因为我使用postman测试是通过的，于是就找到了以下这篇文章，解决了我的问题：

微信小程序开发中网络请求必不可少.GET .POST请求是最常用的.GET请求，POST请求的时候有好几个坑.我已经为大家填好了.

之前的.GET 请求都是如下这样写：（给大家举个例子）

	wx.request({
	      url: url,
	      data: {
	        teacherid: teacherid
	      },
	      header: {
	        'content-type': 'application/json'
	      },
	      success: (res) => {
	        // console.log(res.data);
	        this.setData({
	          testpaper: res.data.testpaper,
	          teacher : res.data.teacher
	        });
	      }
	    })

但是post方式提交的话就有所改变了，给大家列出以下几点注意事项：

1. `'Content-Type': 'application/json'`用在get请求中没问题。POST请求就不好使了.需要改成 :  `"Content-Type": "application/x-www-form-urlencoded"`

2. 加上 method: `"POST"`

3. `data: { answer : { "a":10,"b":8,"c":6 }  } `写成json格式这样也是请求不到数据的.需要转格式.

这里我用`JSON.Stringify()` 将json对象转换成json字符串格式

部分代码分享给大家，这里answer与student 都是json对象格式需要转换

	wx.request({
	      url : "https://www.",
	      method: "POST",
	      data: {
	        answer : JSON.stringify(this.data.answer),
	        score : _score,
	        pjid : this.data.pj.pjid,
	        testpaperid : this.data.pj.testpaperid,
	        student : JSON.stringify(this.data.student),
	        message : this.data.message
	      },
	      header: {
	        "Content-Type": "application/x-www-form-urlencoded"
	      },
	      success: function (res) {
	        console.log(res.data);
	        wx.navigateBack({
	          delta: 1  //小程序关闭当前页面返回上一页面
	        })
	        wx.showToast({
	          title: '评教成功！',
	          icon: 'success',
	          duration: 2000
	        })
	      },
	    })

原文链接：https://blog.csdn.net/lff1123/article/details/80254282