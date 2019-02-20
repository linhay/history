---
title: Khala路由组件使用
date: 2019-02-20
categories: [iOS]
tags: [iOS,Khala,组件]
---

![img](https://camo.githubusercontent.com/34ea19eed8f8db82a48c72ccbe438628eabc13d9/68747470733a2f2f732e6c696e6865792e636f6d2f4b68616c612d6c6f676f2e706e67)

## 前言

在模组化的过程中,业务模块间的通信往往是处理最多的.与其应运而生的解决方案有以下几种:

1. 提前注册服务
2. `Runtime` 动态发现服务

前辈们在借鉴 web 服务路由设计之后,将服务绑定至固定规则的 URL 上.

1. [**CTMediator**](https://github.com/casatwy/CTMediator): `Target-Action` 形式设计的路由组件.
2. 其他以统一注册形式设计的路由.

<!-- more -->

## 前情提要

> 什么是 Target-Action?

ans: 目标-行为模式,它贯穿于iOS开发始终, 最常见的便是:

```swift
UIButton().addTarget(target: target, action: action, for: event)	
```

> 什么是 Target-Action 形式的路由?

ans: 在触发路由路径时, 通过 `Runtime`机制来发现具体服务,并执行.

相对于提前统一注册的路由,相当于懒加载.

# [**Khala**](https://github.com/linhay/Khala) 路由组件使用

## 一. 编写 服务类(路由类)

1. 继承自`NSObject`: `Runtime`机制本身便是基于 `NSObject`.
2. 声明`@objc(ClassName)`: 由于Xcode 8.0之后在编译时会移除未显式使用的swift类与函数,所以需要加此声明.(服务类不建议在任何模块实例化,会破坏设计逻辑)
3. 声明` @objcMembers`: 在`swift 4.0` 中继承 `NSObject` 的 `swift class ` 不再默认全部桥接到 `objective-c`,如果我们想要使用的话我们就需要在class前面加上 `@objcMembers` 这么一个关键字.
4. 示例如下如下: 

```swift
@objc(AModule) @objcMembers
class AModule: NSObject { }
```

## 二. 编写 服务函数(路由函数)

1. 参数类型限制: 只能使用单个`KhalaInfo(集合类型)`与多个 `KhalaClosure(闭包类型)`

2. 第一个参数需要匿名: 方便查找函数, 非匿名`swift`函数桥接至 `objective-c`时会形成`funNameWithParam`结构.(`Khala`前期在此花费时间较多, 最后还是采用该折中方案.)

3. 示例如下如下: 

   ```swift
   @objc(AModule) @objcMembers
   class AModule: NSObject,UIApplicationDelegate {
    
     func vc() -> UIViewController {
       let vc = UIViewController()
       vc.view.backgroundColor = UIColor.red
       return vc
     }
       
    func action(_ info: KhalaInfo, success: KhalaClosure, failure: KhalaClosure) {
       success(["success": #function])
       failure(["failure": #function])
     }
       
   }
   ```

## 三. 调用

1. 通用型调用

   ```swift
    Khala(str: "kf://AModule/forClosures")?.call(blocks: { (item) in
      print("forClosures block3:", item)
    },{ (item) in
      print("forClosure block4:", item)
    })
   
   let value = Khala(str: "kl://AModule/doSomething")?.call()
   ```

2. `UIKit`特例化调用:

   ```swift
   guard let vc = Khala(str: "kl://BModule/vc?style=0")?.viewController else { return }
   self.navigationController?.pushViewController(vc, animated: true)
   ```

## 四. URL重写 (KhalaRewrite)

该部分设计来源自nginx:  在某些场景下需要对 URL 进行转化与拦截, 例如:

1. 服务类与服务函数存在前缀.

2. 接收非标准 URL .

3. URL重定向: 页面的动态升降级, 示例如下: 

   ```swift
   let filter = KhalaRewriteFilter {
         if $0.url.host == "AModule" {
           var urlComponents = URLComponents(url: $0.url, resolvingAgainstBaseURL: true)!
           urlComponents.host = "BModule"
           $0.url = urlComponents.url!
         }
         return $0
       }
   
   Khala.rewrite.add(filter: filter)
   
   // "kl://AModule/doSomething" => "kl://BModule/doSomething"
   let value = Khala(str: "kl://AModule/doSomething")?.call()
   print(value ?? "nil")
   ```

## 五. UIApplicationDelegate 生命周期分发

部分组件往往依赖于主工程中的`AppDelegate`中部分函数.

1. 在`Khala`中,需要显式的在主工程中的`AppDelegate`调用与处理相关逻辑.
2. 服务类需要遵守`UIApplicationDelegate`协议.

主工程`AppDelegate`:

```swift
@UIApplicationMain
class AppDelegate: UIResponder,UIApplicationDelegate {

  var window: UIWindow?

  func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    let list = Khala.appDelegate.application(application, didFinishLaunchingWithOptions: launchOptions)
    return true
  }
    
}
```

组件中服务类:

```swift
@objc(AModule) @objcMembers
class AModule: NSObject,UIApplicationDelegate {
  
  func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    print("AModule.didFinishLaunchingWithOptions")
    return true
  }
  
}
```

## 六. **日志模块:** [**KhalaHistory**](https://linhay.github.io/Khala/Protocols/KhalaHistory.html)

每一份url请求都将记录至日志文件中, 可以在适当的时候提供开发者便利.

1. 开启日志(默认关闭)

   ```
   Khala.isEnabledLog = true
   // or 
   Khala.history.isEnabled = true
   ```

2. 文件路径: `/Documents/khala/logs/`

3. 文件内容: 日期 + 时间 + URL + 参数

```
2018-12-01 02:06:54  kl://SwiftClass/double?  {"test":"666"}
2018-12-01 02:06:54  kl://SwiftClass/double  {"test":"666"}
```

## 七. 扩展机制: [**KhalaStore**](https://linhay.github.io/Khala/Classes.html#/c:@M@Khala@objc(cs)KhalaStore)

***khala*** 库中提供了一个空置的类[***KhalaStore***]用于盛放**路由函数**对应的本地函数.来简化本地调用复杂度的问题.

```
extension KhalaStore { 
 class func aModule_server(value: Int) -> Int {
    return Khala(str: "kf://AModule/server", params: ["value": value])!.call() as! Int
  }
}
  
@objc(AModule) @objcMembers
class AModule: NSObject {
 func server(_ info: [String: Any]) -> Int {
    return info["value"] as? Int ?? 0
  }
}

let value = KhalaStore.aModule_server(value: 46)
```

> ps: KhalaStore 扩展文件建议统一放置.

## 八. 断言机制

为方便开发者使用,添加了部分场景下断言机制,示例:

```
khala.iOS Fatal error: [Khala] 未在[AModule]中匹配到函数[server], 请查看函数列表:
0: init
1: doSomething:
2: vc
```

关闭断言(默认开启):

```
Khala.isEnabledAssert = false
```

## 九. **缓存机制:** [**KhalaClass.cache**](https://linhay.github.io/Khala/Classes/PseudoClass.html#/c:@M@Khala@objc(cs)PseudoClass(cpy)cache)

- 当路由第一次调用/注册路由类时,该路由类将被缓存至 [**KhalaClass.cache**](https://linhay.github.io/Khala/Classes/PseudoClass.html#/c:@M@Khala@objc(cs)PseudoClass(cpy)cache) 中, 以提高二次查找性能.

- 当路由类实例化时,该路由类中的函数列表将被缓存至 [**KhalaClass().methodLists**](https://linhay.github.io/Khala/Classes/PseudoClass.html#/c:@M@Khala@objc(cs)PseudoClass(py)methodLists)中, 以提高查找性能.

  


## 本司基于路由实现的业务架构

> 仅供参考, 合适才是最好的.

![](https://s.linhey.com/iOS-%E6%9E%B6%E6%9E%84%E8%AE%BE%E8%AE%A1-02.png!m1)

## 相关链接

[**Marmot-iOS**](https://github.com/Marmot-App/Marmot-iOS): 基于 [**Khala**](https://github.com/linhay/Khala) 设计的 hybird(`WKWebview` <=> `Native` ) 通信组件.

## 参考

> [苹果核 - 解耦神器 —— 统跳协议和Rewrite引擎.](http://pingguohe.net/2015/11/24/Navigator-and-Rewrite.html)
>
> [蘑菇街路由组件: MGJRouter](https://github.com/meili/MGJRouter) / [阿里开源组件化方案: BeeHive](https://github.com/alibaba/BeeHive)
>
> [casa: iOS应用架构谈 组件化方案](https://casatwy.com/iOS-Modulization.html) 
>
> [bang: iOS 组件化方案探索](https://blog.cnbang.net/tech/3080/)

