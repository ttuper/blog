
### nginx配置片段讲解：

	server {
		# 1、监听的端口号
        listen       80; 
		# 2、监听的地址
        server_name  127.0.0.1; 
		# 3、工作目录
		root   E:\project\xjb\git\bjnews-mp\api\web;
		# 4、配置网站根目录请求路由
		location / {
            index  index.php index.html index.htm;
            if (!-e $request_filename) {
                rewrite ^/(.*)$ /index.php?r=$1 last;
            }
        }
		# 5、配置访问php后缀文件时的路由
        location ~ \.php$ {
            root           E:\project\xjb\git\bjnews-mp\api\web;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
    }

该配置文件工作目录的root目录配置的是以Yii框架为前提的启动脚本所在的web目录为根目录

## php重写（隐藏掉index.php）

**原url地址：http://127.0.0.1/index.php?r=controller/action&param1=xx&param2=xx&...**

**重写后地址：http://127.0.0.1/controller/action?param1=xxx&param2=xxx&...**

1、**`location \`说明**

`location /` 表示当访问的是网站根目录时的路由方案，例如，当访问127.0.0.1时，默认先去找index.php，如果没有index.php就向下找index.html，index.html再没有就找index.htm，所以，`location`中的`index`的意思就是，当不写明请求的是哪个文件时，默认访问的文件。

	if (!-e $request_filename) {
    	rewrite ^/(.*)$ /index.php?r=$1 last;
    }

这一段的意思是，如果访问的文件不存在，那么就执行重写rewrite，`^/(.*)$`正则表达式的意思是网站根目录后面所有的内容，例如访问的`http://127.0.0.1/controller/action?param1=xxx&param2=xxx&...`，那么正则匹配的就是这一段`controller/action?param1=xxx&param2=xxx&...`。匹配上后重写到`/index.php?r=$1`，`$1`就是正则表达式中`()`中匹配到的内容，即`controller/action?param1=xxx&param2=xxx&...`，所以，咱们访问的是`http://127.0.0.1/controller/action?param1=xxx&param2=xxx&...`，根据重写规则，就会去请求`http://127.0.0.1/index.php?r=controller/action&param1=xx&param2=xx&...`。这时候，重写就完成了，index.php也就去掉了。

2、**`location ~ \.php$`说明**

上面进行路由重写后，浏览器中输入的是`http://127.0.0.1/controller/action?param1=xxx&param2=xxx&...`，实际上是去访问`http://127.0.0.1/index.php?r=controller/action&param1=xx&param2=xx&...`，这时候就需要确认下当访问php文件时是如何操作的了。

`~ \.php$`该段的意思是匹配以.php结尾的文件，并且区分大小写，所以，当访问扩展名为php的文件时，就走该段代码。

`fastcgi_pass 127.0.0.1:9000`表示监听的9000端口，该端口就是php-fpm进程的端口号。

总之，路由重写的关键是正则表达式，只要正则表达式写的没问题，想怎么重写就怎么重写了。