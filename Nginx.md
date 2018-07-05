---
title: nginx.conf 配置
date: 2018-04-09
categories: "server"
tags: 
     - nginx
     - server
description: nginx.conf 
---

# nginx.conf 配置

#### 1. 文件划分

nginx安装结束后,由于各路文件分散在不同文件夹中.笔者认为将其配置放置于一处方便管理.

在用户目录下新建以下文件夹与文件.

```shell
nginx/
|-- conf
|   |-- api.conf # api 域名配置
|   |-- www.conf # www 域名配置
|-- log
|   |-- access.log	# 从 /var/log/nginx/access.log 拷贝
|   |-- error.log	# 从 /var/log/nginx/error.log 拷贝
|-- sites-enabled
|   |-- default.conf # 从 /etc/nginx/sites-enabled/default.conf 拷贝
|-- ssl
|   |-- linhey.crt # ssl 证书
|   |-- linhey.key # ssl 证书
|-- html
    |-- 404
        |-- index.html # 404 展示页面
  
```

#### 2. nginx.conf 修改

 1. 需要配置 `/etc/nginx/nginx.conf `文件指向以上目录结构.

    ```shell
    http {
    	# Logging Settings
    	access_log /root/nginx/log/access.log;
    	error_log /root/nginx/log/error.log;
    
    	# Virtual Host Configs
    	include /root/nginx/conf/*.conf;
    	include /root/nginx/sites-enabled/*;
    }
    ```

	2. 需要配置` /nginx/sites-enabled/default.conf`文件404指向.

    ```shell
    	root /root/nginx/html/404;
    	index index.html index.htm;
    ```

    





