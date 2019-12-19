**背景**

数据库资源做过一次迁移，由本地机房mysql迁移到京东云RDS，数据库名为db，之前的app客户端用的数据库是该db，但是为了安全起见，京东云的RDS数据库设置只能内网连接，无法访问数据库所在的服务器，后来还是上级领导提示，可以用nginx进行反向代理，“nginx可以代理一切”，至理名言啊。

因此，可以用过一台两边都能访问的服务器作为反向代理服务器，使用nginx进行反向代理。

**后续**

假设用作反向代理的服务器的ip为：10.10.10.1

以下为该机器上nginx的反向代理配置部分：

	stream {
	    server {
	        listen  3308;
	        proxy_timeout   525600m; //两个成功的读或写操作之间的间隔时间，超出这个时间连接会关闭。这里设置为1年
	        proxy_pass  mysql-cn-north-1-xxxx.rds.jdcloud.com:3306;
	    }
	}

该stream与http同级

`listen 3308` 我这边用3308端口作为监听的端口号

`proxy_timeout 1566662400m` 设置超时时间，默认为10m。应用超过10分总没有数据库操作，nginx会关闭这个server，下次访问就会报错。

`proxy_pass mysql-cn-north-1-xxxx.rds.jdcloud.com:3306` 反向代理到哪个实例及端口上，该处为反向代理到实例名为 mysql-cn-north-1-xxxx.rds.jdcloud.com 的3306端口上，即数据库的端口。

然后，检查下nginx.conf的配置是都正确

`/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf`

最后，平滑重启该nginx服务

`/usr/local/nginx/sbin/nginx -s reload`

后面就可以使用ip:10.10.10.1 、端口 3306 及该数据库给分配的数据库账号密码进行远程连接了。

**后记**

深入研究下nginx 