---
title: nginx配置
date: 2018-04-09
categories: "server"
tags: 
     - nginx
     - server
description: Nginx配置https与端口映射.
---

# nginx配置

### https设置

1. 证书申请

2. 一些注意事项

   1. 云厂商**安全组**配置相应**入站端口规则**
   2. 二级域名也需要添加至dns解析中
   3. dv型ssl证书二级域名页需要单独申请

3. nginx配置

   1. 将相应证书放置于/etc/nginx目录中
   2. nginx.conf添加443端口配置

   ```ruby
   server {
   	listen 443;
   	server_name www.linhey.com; #填写绑定证书的域名
   	ssl on;
   	ssl_certificate 1_www.linhey.com_bundle.crt;
   	ssl_certificate_key 2_www.linhey.com.key;
   	ssl_session_timeout 5m;
   	ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
   	ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
   	ssl_prefer_server_ciphers on;
   	location / {
   		proxy_redirect off;
   		proxy_pass http://localhost:3000;
   		}
   	}
   ```

### http自动跳转https

```ruby
    server{
        listen 80;
        server_name www.linhey.com;
        # 转向对应的https链接
        rewrite ^(.*) https://$server_name$request_uri;
    }

    server {
        listen 443;
        server_name www.linhey.com;
        ssl on;
        ssl_certificate 1_www.linhey.com_bundle.crt;
        ssl_certificate_key 2_www.linhey.com.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
        ssl_prefer_server_ciphers on;
        location / {
            proxy_redirect off;
            proxy_pass http://localhost:3000;
        }
    }
```

### 静态资源配置

```ruby
 server {
        listen 443;
        server_name www.linhey.com;
     
     #多个 location 依次匹配,直至匹配成功.
        location ^~ /rn/ {
            autoindex on;
            autoindex_exact_size off;
            autoindex_localtime on;
            # 优先加载索引文件
            index index.html;
            root /root/rn/;  
        }
     
		location ^~ / {
            autoindex on;
            autoindex_exact_size off;
            autoindex_localtime on;
            # 优先加载索引文件
            index index.html;
            root /root/html/;  
        }
    }
```



### 端口映射

```ruby
 server {
        listen 443;
        server_name www.linhey.com;
        location / {
            proxy_redirect off;
            # 映射本地端口
            proxy_pass http://localhost:3000;
        }
    }
```

### 多端口映射

```ruby
 server {
        listen 443;
        server_name www.linhey.com;
        location / {
            proxy_redirect off;
			# 映射本地端口
            proxy_pass http://localhost:3000;
        }
    }

 server {
        listen 443;
        server_name api.linhey.com;
        location / {
            proxy_redirect off;
			# 映射本地端口
            proxy_pass http://localhost:3001;
        }
    }
```

### error

1.Permission denied

```
user nginx; => user root;
```

