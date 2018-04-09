---
title: hexo配置与自动化更新
date: 2018-03-08 
categories: "docker"
tags: 
     - hexo
     - docker
     - webhook
description: 采用docker构建hexo服务.
---

# hexo配置与自动化更新

- [hexo安装](https://github.com/hexojs/hexo)
- [hexo-Next主题安装](https://github.com/theme-next/hexo-theme-next)
- [NexT官网](http://theme-next.iissnan.com/)
- [Next配置](http://shenzekun.cn/hexo的next主题个性化配置教程.html)

## hexo - docker

### [Docker安装](https://www.docker-cn.com/community-edition)

### Github设置

1. 由于ssh/password方式较为繁琐.以下采用[Github: Personal access tokens](https://github.com/settings/tokens)拉取仓库.

   🐞:

   - Personal access tokens 只会首次出现,注意保存.
   - Personal access tokens 只需勾选`repo`权限.

2. 仓库划分:

   - 文章仓库: posts
   - hexo仓库: hexo
     - 为了保证线上与mac上的环境一致,我将`.gitignore`中`node_modules` 移除.


### Ubuntu

1. **文件目录**:

   ```shell
   .
   ├── hexo
   ├── Dockerfile
   ├── posts
   │   ├── .git
   │   ├── LICENSE
   │   └── xxxx.md
   ├── update.sh
   └── start.sh
   ```

2. **Dockerfile**:

   ```dockerfile
   # 基础镜像
   FROM node:latest
   # 作者
   MAINTAINER linhay 
   # 安装hexo
   RUN npm install -g hexo-cli
   # 拉取 <---需要修改--->
   RUN git clone https://[tocken]@github.com/[you name]/[repo name].git
   # 切换目录
   WORKDIR Hexo
   # 添加权限与安装npm
   RUN chmod +x /Hexo/ && npm install
   # 挂载md目录
   VOLUME [ "/Hexo/source/_posts"]
   # 端口暴露 <---hexo默认4000端口--->
   EXPOSE 4000
   # 运行 hexo
   CMD ["hexo","server"]
   ```

3. **start.sh**

   ```shell
   # 构建image
   docker build -t hexo .
   # 启动容器
   docker run -d --name hexo -p 9000:9000 -v $PWD/posts:/Hexo/source/_posts hexo
   # -d 守护态运行
   # --name 容器别名
   # -p 端口映射,[宿主机端口:容器端口]
   # -v 文件挂载,[宿主机绝对路径:容器绝对路径]
   # hexo: 上面构建的image.
   ```

4. update.sh

   ```shell
   # 切换目录
   cd ~/hexo/posts
   # 移除更改
   git checkout .
   # 拉取仓库更新
   git pull
   # 重新生成网站资源
   docker exec -it hexo hexo d -g
   ```


### 启动!

1. **执行以下sh文件:**

   ```shell
   cd ~/hexo
   sh start.sh
   # 结束就可以访问对应网址了~
   ```

2. **博客更新**

   ```shell
   cd ~/hexo
   sh update.sh
   # 结束就刷新网站资源了~
   ```

3. **更新hexo仓库**:

   ```shell
   #1
   docker exec -it hexo git pull
   #2 进入容器
   docker exec -it hexo /bin/bash
   git checkout .
   git pull
   ```

   ​


