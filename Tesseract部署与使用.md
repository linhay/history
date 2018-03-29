---
title: Tesseract部署
date: 2018-01-02
categories: "图像识别"
tags: 
     - tesseract
     - orc
description: 安装Tesseract提供orc服务.
---

#Tesseract部署 

### 安装(Mac OS)

- 使用 brew 安装

  ```shell
  brew install tesseract
  ```

-  加载语言包(默认只有英文包: eng)

   - 简体中文包

     ```shell
     cd /usr/local/Cellar/tesseract/{version}/share/tessdata
     wget https://github.com/tesseract-ocr/tessdata/raw/master/chi_sim.traineddata
     # 其他语言包链接: https://github.com/tesseract-ocr/tessdata
     ```

   - 安装所有的语言包

     ```shell
     brew install tesseract --all-languages
     ```

### 其他平台安装参考

- [Install Tesseract via pre-built binary package](https://github.com/tesseract-ocr/tesseract/wiki)
- [build it from source](https://github.com/tesseract-ocr/tesseract/wiki/Compiling)

### 使用

​	这里只讲一下Tesseract的基本用法,由于Tesseract只提供命令行工具,我们就在终端中使用.


- 查看支持语言列表

  ```shell
  tesseract --list-langs

  # output: 
  List of available languages (3):
  chi_sim
  eng
  osd
  ```

  我这边安装了简体中文/英文语言包.

- 识别

  ```shell
  tesseract input.png output -l chi_sim -psm 0
  input.png  # 待识别图片路径
  output	   # 识别后的文件路径(输出为txt格式,不需要加后缀名)
  -l 	 # 可选参数,选择识别时所用的字库,默认为eng
  -psm # 可选参数,默认值为3:
   0 = 只进行定向和脚本检测（OSD）
   1 = 通过OSD进行页面自动分割
   2 = 自动分割，但没有OSD，或OCR
   3 = 全自动分割，但没有OSD（默认）
   4 = 假设待识别图片是一列的文本
   5 = 假设待识别图片是一个统一的垂直对齐的文本块
   6 = 假设待识别图片是一个统一的文本块
   7 = 把图像作为一个单一的文本行
   8 = 把图像当作一个字
   9 = 把图像作为一个字在一个圆圈中
  10 = 把图像当作一个单独的字符
  ```

  ​

  ​