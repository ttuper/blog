在商城 APP 开发时，在与客户端联调 API 接口的过程中，我们发现，在 PHP 的 $_SERVER 超全局变量中某些自定义的 HEADER 字段居然获取不到。通过抓包工具查看数据包，该自定义头的确是存在的。
后来通过调试我们发现，根本原因是客户端错误地将字段名中的中划线写成了下划线。例如，应该是 `X-ACCESS-TOKEN`，却被写成了 `X_ACCESS_TOKEN`。

问题本身很好解决。然而我们想要知道，服务器为何要对字段名中使用了下划线的头视而不见呢？并且，不管是 Apache 还是 Nginx，对于这样的情况，都不约而同地采取了一样的策略。

在 RFC 2616 4.2 节中，有如下一段话：  
Request (section 5) and Response (section 6) messages use the generic message format of RFC 822 [9] for transferring entities (the payload of the message).
这段话的意思，就是说 HTTP/1.1 的请求和响应消息使用 RFC 822 中的通用消息格式来传输实体（消息载荷）。

在 RFC 822 3.1.2 节中，对于消息格式的说明，有这样一句话：  
The  field-name must be composed of printable ASCII characters (i.e., characters that  have  values  between  33.  and  126., decimal, except colon).  
也就是说，HEADER 字段名可以可由可打印的 ASCII 字符组成（也就是十进制值在 33 和 126 之间的字符，不含冒号）。  
不含冒号很容易理解，因为 `Field-Name` 和 `Value` 之间需要用冒号分割。然而，我们通过查询 ASCII 码表可知，下划线的十进制 ASCII 值为 95，也在此范围之内！

其实，在 HEADER 字段名中使用下划线其实是合法的、符合 HTTP 标准的。服务器之所以要默认禁止使用是因为 CGI 历史遗留问题。下划线和中划线都为会被映射为 CGI 系统变量中名中的下划线，这样容易引起混淆。

在 nginx 服务器中，通过显式地设置 `underscores_in_headers on` 可以开启在字段名中使用下划线。默认该选项是关闭的，所以在默认情况下，所有包含下划线的字段名都会被丢弃。

在我们的开发机中的确也开启了这个选项，为啥还是不能拿到字段名中包含下划线的 HEADER 呢？这是因为我们访问这台开发机的时候，前面还有一层代理服务器，而这台代理服务器没有开启相关选项，导致这种 HEADER 被代理服务器直接丢弃。因此也强烈建议不要在 HEADER 的 Field-Name 中使用下划线。

原链接：[为何禁止在 HTTP HEADER 的字段名中使用下划线](http://www.fising.cn/2017/10/为何禁止在-http-header-的字段名中使用下划线.shtml)


