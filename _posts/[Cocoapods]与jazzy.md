---
title: Cocoapods 与 jazzy
date: 2018-06-30
categories: [cocoapods]
tags: [cocoapods,jazzy,github,api]
description: jazzy 是一款可以为 Swift 和 Objective-C 代码产生具有 Apple 风格的代码文档工具.
---

# Cocoapod 与 jazzy

先上效果图 [链接地址](https://linhay.github.io/SPKit/):

![](https://s.linhey.com/jazzy-0.png)



## 安装与使用

1. 安装 [**jazzy**](https://github.com/realm/jazzy)

   ```shell
   [sudo] gem install jazzy
   ```

2. 文档生成

   ```shell
   cd [项目目录]
   # 默认在当前目录下生成 docs/
   # 这里导出到Github.io仓库目录
   jazzy --podspec [xxx.podspec] --output [输出路径]
   ```

## Github.io

[Github.io申请](https://pages.github.com/)

由于普通Github无法解析html文件,笔者这边采用了Github.io来托管开源项目 API 文档.




