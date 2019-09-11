title: springboot部署https
date: 2019-08-29 10:18:57
categories: spring

tags: [nginx,https,springboot] 
---

很多场景都要求使用`https`了，但是一般开发都是`http`环境，改造`https`有很多方法，这里介绍一种最好用的也是实际生产最多见的一种方式。

<!-- more -->

### 下载ssl证书

你需要申请一个`ssl`证书，证书有很多种，学习的情况下，去阿里云买个免费的就行了，下载适合`nginx`使用的证书，会得到一个压缩包，里面有2个证书

### 配置nginx

得到证书后上传到服务器，放到你想放的位置，在`nginx`添加相关配置

```nginx
	#Tomcat 8080端口
	upstream tomcat_8088{
		server    127.0.0.1:8088  weight=1;
	}
		
	#将所有http协议内容重定向到https协议
	server {
		listen 80;
		server_name xxx.com;#你的域名
		rewrite ^ https://$server_name$request_uri? permanent;
	}

	#https协议
	server {
		listen 443;
		server_name xxx.com;#你的域名
		
		# letsencrypt生成的文件
		ssl on;
		ssl_certificate /usr/local/nginx/cert/xxx.pem;#你的证书
		ssl_certificate_key /usr/local/nginx/cert/xxx.key;#你的证书

		ssl_session_timeout 1d;
		ssl_session_cache shared:SSL:50m;
		ssl_session_tickets on;

		ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
		# 一般推荐使用的ssl_ciphers值: https://wiki.mozilla.org/Security/Server_Side_TLS
		ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128:AES256:AES:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK';
		ssl_prefer_server_ciphers on;
		
		# 代理tomcat
		location / {   
			proxy_set_header	Host				$http_host;  			
			proxy_set_header	X-Real-IP			$remote_addr;     
			proxy_set_header	X-Forwarded-For		$proxy_add_x_forwarded_for;     
			proxy_set_header	Cookie				$http_cookie;
			# 重定向会出现跳转http的问题
        	 proxy_set_header 	X-Forwarded-Proto 	$scheme;
			proxy_pass			http://tomcat_8088;
			#proxy_redirect		default;
		}

	}
```

重定向会出现`http`的问题必须添加

>  proxy_set_header 	X-Forwarded-Proto 	$scheme;

### 修改springboot相关配置

```yaml
server:
    # 下面2项配置结合nginx防止重定向到http
    use-forward-headers: true
    tomcat:
        protocol-header: X-Forwarded-Proto
```

是不是很简单的，但是网上的教程一般都很笼统或者根本就不全