爬虫工具：神箭手云爬虫

服务器环境：Linux + php7.1.19 + mysql8.0

问题：

使用神箭手开启爬虫调用后台接口入库php-fpm日志提示增大max_children、start_server、min_spare_servers、max_spare_servers

解决过程中查遍了网上所有的方法都没有解决，而且测试上不会出现这个问题，只要这个问题一出现，整个服务器就宕机了。

没有去研究神箭手云爬虫如果遇到请求失败，服务器返回500错误之后是怎样处理的，只知道返回500后，神箭手云爬虫返回的日志是过多少秒之后再重试。

后来才发现，爬虫请求后台失败后，会一直占用着一个php-fpm进程，当然不会只有一个请求失败，当多个请求失败后，就会生成多个php-fpm进程，当达到max_children上限值时，就开始宕机了，所以

解决方案就是，在自己后台代码中，充分利用捕获异常机制，try catch 来捕获异常，避免请求失败而夯住一个php-fpm进程