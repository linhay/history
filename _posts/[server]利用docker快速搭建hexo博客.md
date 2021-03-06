---
title: 利用docker快速搭建hexo网站
date: 2019-01-07
categories: [server]
tags: [hexo,server,ubuntu]
description: 
---

![hexo-docker-1](https://s.linhey.com/hexo-docker-1.png)

<!-- more -->

由于近期安利一些同事购买腾讯云低价服务器,承诺了人手送一个可以自动更新的个人网站,顺便优化了一下[本站](https://www.linhey.com/)的部署代码.

### 该篇只介绍如何快速部署,不聊其他,突出一个**快**字!

## 预期

- [x] **整体耗时: 20min**
- [x] **自动更新网站**

## 准备工作

- [x] **ubuntu 云服务器一台**
- [x] **Github 账户一个**

## 开始部署

1. **在 github 上 fork  以下 2 个库:**

   - 样式主题仓库: https://github.com/linhay/hexo_template
   - 网站文章仓库: https://github.com/linhay/hexo_pages_template

2. **修改 hexo_template 仓库中 `./_config.yml` 文件以下内容:**

   ```yaml
   # Site
   title: 标题
   subtitle: 副标题
   avatar: 头像链接URL
   description: 个人简介
   author: 作者名称
   
   # URL
   url: 网站主页URL
   ```

3. **查看修改 hexo_template 仓库中 `./themes/next/_config.yml`文件内容:**

   - 具体查看[ Next 文档](https://theme-next.iissnan.com/theme-settings.html)
   - 笔者配置项则已在文件中用中文标记.

## ubuntu 部署

1. **安装 Git**

   ```bash
   apt-get update
   apt-get install git
   ```

2. **拉取部署脚本仓库**

   ```shell
   cd ~/
   git clone https://github.com/linhay/build.git
   ```

3.  **安装docker环境**

	```shell
	cd ~/build/shells/
	sh install-docker.sh	
	```


4. **修改Dockerfile文件**

   ```shell
   vi ~/build/dockerfiles/hexo/hexo/Dockerfile
   # 将以下2行内容修改为准备步骤的仓库
   # RUN git clone https://github.com/linhay/hexo_template
   # RUN git clone https://github.com/linhay/hexo_pages_template source
   ```

5. **拉起服务**

   ```shell
   cd ~/build/dockerfiles/hexo/
   sh start.sh
   ```

6. **放开9000端口**

   - 腾讯云 -> 云服务器 -> 安全组

7. **访问网站**

   - 打开浏览器访问http://[公网IP]: 9000

## 文章上传

1. **将新的文章上传至 hexo_pages_template 仓库 `_post` 目录下**

2. **等5s再访问网站即可**

   > 有一个 5s 定时拉取的脚本在 docker 中.

## 样式更新

1. **修改 hexo_template 仓库内容**

2. **重启服务**

   ```shell
   cd ~/build/dockerfiles/hexo/
   sh start.sh
   ```

## 进阶

该部分内容不展开了,云主机厂商都有完整的购买与说明了.

1. **80端口:** 80端口为http默认端口,可以缺省.
2. **443端口:** 443端口为http默认端口,可以缺省.
1. **域名购买:** 腾讯云/万网,推荐万网.
2. **DNS解析:** 腾讯云/万网,推荐万网.
3. **https**: 腾讯云有免费的单域名ssl证书可以申请.[**链接**](https://buy.cloud.tencent.com/ssl).
4. **Nginx**:配置反向代理服务来代理80/443端口到9000端口.

## 结尾

临近年关,杂事繁多,献水文一篇.

如果有其他想法不妨在[Github](https://github.com/linhay)联系我.
