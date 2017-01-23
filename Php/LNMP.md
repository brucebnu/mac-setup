# LNMP
	LNMP代表的就是：Linux系统下Nginx+MySQL+PHP这种网站服务器架构。Linux是一类Unix计算机操作系统的统称，是目前最流行的免费操作系统。代表版本有：debian、centos、ubuntu、fedora、gentoo等。Nginx是一个高性能的HTTP和反向代理服务器，也是一个IMAP/POP3/SMTP代理服务器。Mysql是一个小型关系型数据库管理系统。PHP是一种在服务器端执行的嵌入HTML文档的脚本语言。这四种软件均为免费开源软件，组合到一起，成为一个免费、高效、扩展性强的网站服务系统。
	[Nginx Docs](https://nginx.org/en/docs/)
### Install php-fpm and nginx
	提前安装HomeBrew
	使用brew doctor检查是否存在冲突，然后使用brew update && brew upgrade对brew进行升级。

	$ brew tap homebrew/dupes
	$ brew tap homebrew/versions
	$ brew tap homebrew/php

	brew install php56 \
	--without-snmp \
	--without-apache \
	--with-debug \
	--with-fpm \
	--with-intl \
	--with-homebrew-curl \
	--with-homebrew-libxslt \
	--with-homebrew-openssl \
	--with-imap \
	--with-mysql \
	--with-tidy

	$ brew install php56-gearman php56-msgpack php56-memcache php56-memcached php56-mongo  php56-phalcon php56-redis php56-xdebug

	$ brew install nginx

### Config Nginx and php-fpm
	测试Nginx，这里端口为8080，如果需要可以修改监听默认端口。使用80端口，需要加入root组。
	$ curl -IL http://127.0.0.1:8080

	结果类似如下，
	HTTP/1.1 200 OK
	Server: nginx/1.9.1
	Date: Fri, 29 May 2015 14:50:47 GMT
	Content-Type: text/html
	Content-Length: 612
	Last-Modified: Fri, 29 May 2015 14:40:47 GMT
	Connection: keep-alive
	ETag: "5444dea7-264"
	Accept-Ranges: bytes

	操作Nginx
	$ sudo nginx //启动nginx
	$ sudo nginx -s reload|reopen|quit //重新加载|重启|退出

	使用以下命令监测php-fpm是否启动成功：
	$ lsof -Pni4 | grep LISTEN | grep php

	启动成功，有如下类似输出，
	php-fpm     700 bruceniu    6u  IPv4 0x7401c2d6c0081449      0t0  TCP 127.0.0.1:9056 (LISTEN)
	php-fpm     737 bruceniu    0u  IPv4 0x7401c2d6c0081449      0t0  TCP 127.0.0.1:9056 (LISTEN)
	php-fpm     738 bruceniu    0u  IPv4 0x7401c2d6c0081449      0t0  TCP 127.0.0.1:9056 (LISTEN)
	php-fpm     739 bruceniu    0u  IPv4 0x7401c2d6c0081449      0t0  TCP 127.0.0.1:9056 (LISTEN)
	**Note**:在多版本PHP中开发，PHP5.6端口9056，PHP7.0端口9070，方便用于调试。

####修改配置Nginx配置
	vim /usr/local/etc/nginx/nginx.conf
	worker_processes  1;

	error_log  /usr/local/etc/nginx/logs/error.log debug;

	events {
	    worker_connections  1024;
	}

	http {
	    include             mime.types;
	    default_type        application/octet-stream;

	    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
	                      '$status $body_bytes_sent "$http_referer" '
	                      '"$http_user_agent" "$http_x_forwarded_for"';

	    access_log  /usr/local/etc/nginx/logs/access.log  main;

	    sendfile            on;

	    keepalive_timeout   65;

	    index index.html index.php;

	    include /usr/local/etc/nginx/sites-enabled/*;
	}

####php-fpm配置
	vim /usr/local/ect/nginx/conf.d/php-fpm

	location ~ \.php$ {
	    try_files      $uri = 404;
	    fastcgi_pass   127.0.0.1:9000;
	    fastcgi_index  index.php;
	    fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
	    include        fastcgi_params;
	}

####站点配置文件
	vim /usr/local/ect/nginx/sites-enabled/default
	server {
	    listen       80;
	    server_name  localhost;
	    root       /var/www/;

	    access_log  /usr/local/etc/nginx/logs/default.access.log  main;

	    location / {
	        include   /usr/local/etc/nginx/conf.d/php-fpm;
	    }

	    location = /info {
	        allow   127.0.0.1;
	        deny    all;
	        rewrite (.*) /.info.php;
	    }

	    error_page  404     /404.html;
	    error_page  403     /403.html;
	}



