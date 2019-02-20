---
title: 打造合适的App&H5交互框架
date: 2019-01-15
categories: [ios]
tags: [ios,webview,vue,javascript]
---



<!-- more -->

## 前言

介绍 webview 与 h5 通信原理的文章在网络上已经很多了,这次我们来设计打造一套优雅的交互框架.

## 相关接口

> 首先快速过一遍交互相关的api

- **执行 JavaScript:**

  ```swift
  webview.evaluateJavaScript("js code", completionHandler: { (result, error) in })
  ```

- **JavaScript 数据发送至 WKWebview:**

  1. 挂载函数至window下:

     ```swift
     webview.configuration.userContentController.add(self, name: "ios")
     ```

  2. JavaScript发送数据:

     ```js
     window.webkit.messageHandlers.marmot.postMessage({value: "data"})
     ```

  3. WKWebview接收数据:

     ```swift
     extension <#CustomClass#>: WKScriptMessageHandler {
     public func userContentController(_ userContentController: 			WKUserContentController, didReceive message: WKScriptMessage) {
         guard let body = message.body as? [String: Any] else { return }
     	// body == ["value": "data"]
       }
     }
     ```

## 定义框架特性

1. H5开发体验优先.
2. Native端开发体验.
3. 覆盖多种通信方式.
4. 测试覆盖




