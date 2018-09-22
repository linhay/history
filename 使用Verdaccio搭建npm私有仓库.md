---
title: 使用Verdaccio搭建npm私有仓库
date: 2018-09-20
categories: [node]
tags: [docker,node,npm,verdaccio]
description: 作者本身计划是采用Sinopia来作为服务,可惜Sinopia作者已经在三年前不维护该库.Verdaccio是一个轻量级的私有NPM的Registry(派生于Sinopia).本文主要介绍利用docker中配置与启动Verdaccio服务.
---


![verdaccio](https://verdaccio.org/img/logo/banner/png/verdaccio-banner@2x.png)



## docker-compose

1. ###[ 🌰 Github 地址]()

2. ### 目录结构

   ```shell
   .
   ├── LICENSE
   ├── README.md
   ├── conf
   │   ├── config.yaml #配置文件
   │   └── htpasswd # 用户管理文件
   ├── docker-compose.yml 
   ├── plugins	# 插件位置
   ├── storage # 数据存放
   └── update.sh # 启动/重启服务
   ```

3. ### docker-compose的安装与操作命令

      - 官方网址教程: https://docs.docker.com/compose/install/

        - linux安装:

          ```shell
          sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
          
          sudo chmod +x /usr/local/bin/docker-compose
          
          docker-compose --version
          
          print: --------------------------------------
          docker-compose version 1.22.0, build f46880fe
          ```


   - 启动容器

     ```shell
     docker-compose -f "docker-compose.yml" up -d --build
     ```

   - 移除容器

     ```shell
     docker-compose -f "docker-compose.yml" down
     ```

4. ### 快速启动!

   - 使用默认配置的`docker-compose`启动容器:

     ```yaml
     version: '3'
     services:
       verdaccio:
         container_name: verdaccio
         image: verdaccio/verdaccio:3.8.0
         ports:
           - '4873:4873'
     ```

   - 在浏览器查看`http://127.0.0.1:4873`:

     按照网页指引使用npm添加用户即可登录与上传包.

     ![html](https://s.linhey.com/verdaccio-html.png)

5. ### 更多配置

   - 更多配置的docker-compose文件:

     ```yaml
     version: '3'
     services:
       verdaccio:
         container_name: verdaccio
         image: verdaccio/verdaccio:3.8.0
         ports:
           - '4873:4873'
         restart: always
         environment:
           PORT: 4873
         volumes:
           - ./conf:/verdaccio/conf 
           - ./storage:/verdaccio/storage 
           - ./plugins:/verdaccio/plugins 
      # 挂载卷,否则可能有文件权限问题
      volumes:
       verdaccio:
         driver: local
     ```

   - 编辑`./conf/config.yaml`文件:

     ```yaml
     # 数据存储位置。** Verdaccio 默认使用内置本地文件模式存储。
     storage: /verdaccio/storage
     auth:
       htpasswd:
         # 注册用户列表
         file: ./htpasswd
         # 最大注册数 -1为禁止新用户注册
         max_users: 2
     # 上行链路 是指可以访问到外部包的外部注册服务器地址。
     uplinks:
       # 这里使用淘宝加速npm镜像源.
       taobao:
         url: https://registry.npm.taobao.org/
         # 设置超时
         timeout: 100ms
       # 备用源
       npmjs:
         url: https://registry.npmjs.org/
     packages:
       # 匹配规则从上至下,与nginx类同
       '@t4/*':
         # 限定用户访问
         access: linhey test
         # 定义允许发布的组
         publish: linhey
         # 针对特定的uplink限制查找
         proxy: taobao
       '@*/*':
         # 定义允许访问包的组
         access: $authenticated
         # 定义允许发布的组
         publish: $authenticated
         # 针对特定的uplink限制查找
         proxy: taobao
       '**':
         access: $authenticated
         publish: $authenticated
         # 针对特定的uplink限制查找
         proxy: taobao
     logs:
       - {type: stdout, format: pretty, level: http}
     web:
       enable: true
       # 标签名称
       title: npm.linhey
       # 左上角图标
       logo: https://verdaccio.org/img/logo/banner/png/verdaccio-banner@2x.png
       scope:
     
     ```

   - 权限控制

     无法找到为用户设定组别的选项,目前只能单个用户进行权限管理.

   - https

     作者采用一个全局独立的nginx来做反向代理. `server`中的路径根据自身nginx配置调整.

     ```shell
     server {
         listen 443;
         server_name npm.linhey.com;
         ssl on;
         ssl_certificate /root/nginx/ssl/npm.crt;
         ssl_certificate_key /root/nginx/ssl/npm.key;
         location / {
             proxy_redirect off;
             proxy_pass http://localhost:4873;
         }
     }
     ```

   - errors

     1. Error:` EACCES: permission denied, open '/verdaccio/conf/htpasswd'`

        作者在采用`git`方式部署至`Ubuntu 16.04.2`时出现该问题.

        反复尝试多次,最后定位问题出在外部挂载目录权限上.

        ```shell
        chmod 777 -R conf
        chmod 777 -R storage
        chmod 777 -R plugins
        ```



### 参考:

- [Verdaccio 官网](https://verdaccio.org/zh-CN/)
- [Verdaccio DockerHub](https://hub.docker.com/r/verdaccio/verdaccio/)


