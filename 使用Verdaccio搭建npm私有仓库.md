---
title: 使用Verdaccio搭建npm私有仓库
date: 2018-09-20
categories: [node]
tags: [docker,node,npm]
description: Verdaccio是一个轻量级的私有NPM的Registry.本文主要介绍利用docker中配置与启动Verdaccio服务.
---


![verdaccio](https://verdaccio.org/img/logo/banner/png/verdaccio-banner@2x.png)



## docker-compose

1. 目录结构

2. docker-compose的安装与操作命令

   - 启动容器

     ```shell
     docker-compose -f "docker-compose.yml" up -d --build
     ```

   - 移除容器

     ```shell
     docker-compose -f "docker-compose.yml" down
     ```

3. 编写docker-compose启动Verdaccio

   - 默认配置的`docker-compose`:

     ```yaml
     version: '3'
     services:
       verdaccio:
         container_name: verdaccio
         image: verdaccio/verdaccio:3.8.0
         ports:
           - '4873:4873'
     ```



### 参考:

- [Verdaccio 官网](https://verdaccio.org/zh-CN/)
- [Verdaccio DockerHub](https://hub.docker.com/r/verdaccio/verdaccio/)


