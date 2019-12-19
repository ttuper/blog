### 什么是CGI?

CGI（Common Gateway Interface），公共网关接口，它是Web服务器与外部应用程序（CGI程序）之间传递信息的接口标准。通过CGI接口，Web服务器就能够获取客户端提交的信息，并转交给服务器端的CGI程序处理，最后返回结果给客户端。

也就是说，CGI实际上是一个接口标准。我们通常所说的CGI是指CGI程序，即实现了CGI接口标准的程序。

只要某种语言具有标准输入、输出和环境变量，如perl、php、C等，就可以用来编写CGI程序。

### CGI程序的工作方式：

Web服务器一般只处理静态文件请求（如 jpg、htm、html），如果碰到一个动态脚本请求（如php），web服务器主进程，就fork出一个新的进程来启动CGI程序，也就是说将动态脚本请求交给CGI程序来处理。启动CGI程序需要一个过程，比如，读取配置文件，加载扩展等。CGI程序启动后，就会解析动态脚本，然后将结果返回给Web服务器，最后Web服务器再将结果返回给客户端，刚才fork的进程也会随之关闭。

这样，每次用户请求动态脚本，Web服务器都要重新fork一个新进程，去启动CGI程序，由CGI程序来处理动态脚本，处理完后进程随之关闭。

这种工作方式的效率是非常低下的。

PHP 解释器是否嵌入 Web 服务器进程内部执行

后来，出现了一种比较高效的方式：Web服务器内置模块。例如，apache的mod_php模块。将php解释器做成模块，然后加载到apache服务器中。

这样，apache服务器在启动的时候，就会同时启动php模块。当客户端请求php文件时，apache服务器就不用再fork出一个新进程来启动php解释器，而是直接将php文件交给运行中的php模块处理。显然，这种方式下，效率会比较高。

由于在apache服务器启动时，才会读取php的配置文件，加载php模块，在apache的运行过程中。不会再重新读取php的配置文件。所以，每次我们修改了php的配置文件后，必须重启apache，新的php配置文件才会生效。

mod_php 通过嵌入 PHP 解释器到 Apache 进程中，只能与 Apache 配合使用，而 cgi 和 fast-cgi 以独立的进程的形式出现，只要对应的Ｗeb服务器实现 cgi 或者 fast-cgi 协议，就能够处理 PHP 请求。mod_php 这种嵌入的方式最大的弊端就是内存占用大，不论是否用到 PHP 解释器都会将其加载到内存中，典型的就是处理CSS、JS之类的静态文件是完全没有必要加载解释器。

### 什么是FastCGI？

FastCGI就像是一个常驻（long-live）型的CGI程序，它可以一直运行着。FastCGI程序也可以和Web服务器分别部署在不同的主机上，它还可以接受来自其他Web服务器的请求。

FastCGI也是语言无关的。其主要行为是将CGI解释器进程保持在内存中并因此获得高效的性能。众所周知，CGI解释器的反复加载是CGI性能低下的主要原因。

FastCGI是一种进程管理工具，它可以在内存中管理CGI进程。

FastCGI进程管理器需要单独启动。启动FastCGI后，会生成一个FastCGI主进程和多个子进程（子进程其实就是CGI解释器进程）。

当客户端请求Web服务器上的动态脚本时，Web服务器会将动态脚本通过TCP协议交给FastCGI主进程，FastCGI主进程根据情况，安排一个空闲的子进程来解析动态脚本，处理完成后将结果返回给Web服务器，Web服务器再将结果返回给客户端。该客户端请求处理完毕后，FastCGI子进程并不会随之关闭，而是继续等待主进程安排工作任务。

### FastCGI的重要特点：

1. FastCGI是HTTP服务器和动态脚本语言间通信的接口或者工具。
2. FastCGI优点是把动态语言解析和HTTP服务器分离开来。
3. Nginx、Apache、Lighttpd以及多数动态语言都支持FastCGI。
4. FastCGI接口方式采用C/S架构，分为客户端（HTTP服务器）和服务端（动态语言解析服务器）。
5. PHP动态语言服务端可以启动多个FastCGI的守护进程。
6. HTTP服务器通过FastCGI客户端和动态语言FastCGI服务端通信。

### 原理图及运行过程：

![](https://images2018.cnblogs.com/blog/676372/201804/676372-20180411122930391-1818295640.png)

- Web Server启动时载入FastCGI进程管理器（Apache Module或IIS ISAPI等)
- FastCGI进程管理器自身初始化，启动多个CGI解释器进程(可建多个php-cgi)，并等待来自Web Server的连接。
- 当客户端请求到达Web Server时，FastCGI进程管理器选择并连接到一个CGI解释器。Web server将CGI环境变量和标准输入发送到FastCGI子进程php-cgi。
- FastCGI子进程完成处理后，将标准输出和错误信息从同一连接返回Web Server。当FastCGI子进程关闭连接时，请求便告处理完成。FastCGI子进程接着等待，并处理来自FastCGI进程管理器(运行在Web Server中)的下一个连接。 在CGI模式中，php-cgi在此便退出了

### 什么是php-fpm？

fpm是FastCGI Process Manager的缩写，那么，fpm就是FastCGI进程管理器的简称。

php-fpm就是php中的FastCGI进程管理器。
对于php5.3之前的版本来说，php-fpm是一个第三方的补丁包，旨在将FastCGI进程管理整合进PHP包中。

在php5.3之后的版本中，php-fpm不再是第三方的包，它已经被集成到php的源码中了。php-fpm提供了更好的PHP进程管理方式，可以有效控制内存和进程、可以平滑重载PHP配置，比spawn-fcgi具有更多优点，所以php-fpm被PHP官方收购了。

php-fpm的管理对象是php-cgi。但不能说php-fpm是fastcgi进程的管理器，因为前面说了fastcgi是个协议，似乎没有这么个进程存在，就算存在php-fpm也管理不了他（至少目前是）。 有的说，php-fpm是php内核的一个补丁以前是对的。因为最开始的时候php-fpm没有包含在PHP内核里面，要使用这个功能，需要找到与源码版本相同的php-fpm对内核打补丁，然后再编译。后来PHP内核集成了PHP-FPM之后就方便多了，使用--enalbe-fpm这个编译参数即可。