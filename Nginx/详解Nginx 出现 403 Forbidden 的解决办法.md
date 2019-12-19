Nginx 也是当前流行的一款 轻量级服务器  在日常使用中呢 也会出现一些问题  今天 学习君 在安装配置Nginx的时候就出现了 403 Forbindden 的被禁止访问的错误   网上搜索之后呢 完美解决  这里给大家分享下

话不多说 先粘 上 原版nginx 配置文件代码

	worker_processes 1; 
	  
	events { 
	  worker_connections 1024; 
	} 
	  
	http { 
	  include    mime.types; 
	  default_type application/octet-stream; 
	    
	  sendfile    on; 
	  autoindex    on; 
	  keepalive_timeout 65; 
	 fastcgi_connect_timeout 300; 
	 fastcgi_send_timeout 300; 
	 fastcgi_read_timeout 300; 
	 fastcgi_buffer_size 128k; 
	 fastcgi_buffers 4 128k; 
	 fastcgi_busy_buffers_size 256k; 
	 fastcgi_temp_file_write_size 256k; 
	  
	 #gzip on; 
	 gzip on; 
	 gzip_min_length 1k; 
	 gzip_buffers   4 32k; 
	 gzip_http_version 1.1; 
	 gzip_comp_level 2; 
	 gzip_types    text/plain application/x-javascript text/css application/xml; 
	 gzip_vary on; 
	 gzip_disable "MSIE [1-6]."; 
	  
	 server_names_hash_bucket_size 128; 
	 client_max_body_size   100m;  
	 client_header_buffer_size 256k; 
	 large_client_header_buffers 4 256k; 
	  
	  server { 
	    listen    80; 
	    server_name localhost; 
	    autoindex    on;  #是否允许访问目录 
	  
	    root  "C:/WWW"; 
	    location / { 
	      index index.html index.htm l.php; 
	      #index.php 
	      autoindex on; 
	    } 
	    error_page  500 502 503 504 /50x.html; 
	    location = /50x.html { 
	      root  html; 
	    } 
	  
	    location ~ \.php(.*)$ { 
	      fastcgi_pass  127.0.0.1:9000; 
	      #fastcgi_index index.php; 
	      fastcgi_split_path_info ^((?U).+\.php)(/?.+)$; 
	      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; 
	      fastcgi_param PATH_INFO $fastcgi_path_info; 
	      fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info; 
	      include    fastcgi_params; 
	    } 
	  
	  } 
	  
	include vh_*.conf; 
	  
	} 

这么多配置文件代码(为了便于查看 已经把注释的那些不懂的删除)  学习君也是看不懂的

先说403 出现的原因

**当访问该地址的时候，nginx 会按照 index.html，index.htm ，index.PHP 的先后顺序在根目录中查找文件。如果这三个文件都不存在，那么nginx就会返回403 Forbidden。**

学习君根目录下也是没有这三个文件的 所以直接

	root  "C:/WWW"; 
	 location / { 
	   index index.html index.htm l.php; 
	   #index.php 
	   autoindex on; 
	 }

注释   项目列表就展示出来了
这里要注意
autoindex on; 这个默认是off的 意思是禁止访问目录  需要开启

还有一点推荐大家别把配置项 都写在这个文件当中 不便于管理  

`include vh_*.conf;`

可以引入这样一个域名一个配置文件方便管理

转载自：[详解Nginx 出现 403 Forbidden 的解决办法](https://www.jb51.net/article/121097.htm)