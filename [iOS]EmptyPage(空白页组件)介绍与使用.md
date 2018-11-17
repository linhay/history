---
title: EmptyPage(空白页组件)原理与使用
date: 2018-01-28
updated: 2018-11-17
categories: [ios]
tags: [ios,swift,cocoapods]
description: 一套应用于swift项目的空白页.可利用预置的模板快速构建空白页视图.亦可高度自定义视图搭建炫酷的交互.
---


> app 显示列表内容时, 在某一时刻可能数据为空(等待网络请求/网络请求失败)等, 添加一个空白指示页将有效缓解用户可能造成的焦虑或混乱. 并可以帮助用户处理问题.
>
> 市面上已经有部分成熟的空白页框架,最典型的就是使用[**DZNEmptyDataSet**](https://github.com/dzenbot/DZNEmptyDataSet).
>
> 但是其使用`DZNEmptyDataSetDelegate`,`DZNEmptyDataSetSource`来定制空白页元素,使用时较为繁琐.
>
> 笔者借鉴其原理的基础上,制作了对标框架(单向对标)[**EmptyPage**](https://github.com/linhay/EmptyPage)来简化日常项目开发.

## 前言

> [**EmptyPage**](https://github.com/linhay/EmptyPage) 历时1年, 在我司项目中稳定使用迭代6个版本,算是比较稳定.
>
> 支持UICollectionView & UITableView.
>
> ps: 目前阶段只提供 swift 版本.

![示例](https://s.linhey.com/emptyPage-07.gif)![示例](https://s.linhey.com/emptyPage-08.gif)

## 实现原理

> 该核心部分 作为一个单独的子库 实现, 可使用 以下方式单独引用.
>
> ```ruby
> pod 'EmptyPage/Core'
> ```
>
> 具体代码可查阅 [**Github Link**](https://github.com/linhay/EmptyPage/tree/master/Sources), 超级简单.

1. ##### 为`UIScrollView`添加`emptyView`对象作为空白页实例:

   ```swift
   public extension UIScrollView {
     public var emptyView: UIView?
   }
   ```

2. ##### `Method Swizzling`方式替换掉`UITableView` \ `UICollectionView` 中部分相关函数.以下拿`UITableView` 举例:

   ```swift
   // DZNEmptyDataSet 对 autolayout 项目不太友好. (也可能本人没深度使用...)
   // EmptyPage 
   // UITableView frame 变化相关函数
   open func layoutSubviews()
   open func layoutIfNeeded()
   // 数据源增减相关函数
   open func insertRows(at indexPaths: [IndexPath], with animation: UITableView.RowAnimation)
   open func deleteRows(at indexPaths: [IndexPath], with animation: UITableView.RowAnimation)
   open func insertSections(_ sections: IndexSet, with animation: UITableView.RowAnimation)
   open func deleteSections(_ sections: IndexSet, with animation: UITableView.RowAnimation)
   open func reloadData()
   ```

3. ##### 在数据/frame变化时判断空白页显示与隐藏.

   ```swift
   func setEmptyView(event: () -> ()) {
       oldEmptyView?.removeFromSuperview()
       event()
       guard bounds.width != 0, bounds.height != 0 else { return }
       var isHasRows = false
       let sectionCount = dataSource?.numberOfSections?(in: self) ?? numberOfSections
       for index in 0..<sectionCount {
         if numberOfRows(inSection: index) > 0 {
           isHasRows = true
           break
         }
       }
       isScrollEnabled = isHasRows
       if isHasRows {
         emptyView?.removeFromSuperview()
         return
       }
       guard let view = emptyView else{ return }
       view.frame = bounds
       addSubview(view)
       sendSubview(toBack: view)
     }
   ```

4. **使用**

   ```swift
   UITableView().emptyView = CustomView()
   UICollectionView().emptyView = CustomView()
   ```

   > UITableView().emptyView 第一次被赋值时才会进行 `Method Swizzling` 相关函数.

## 模板视图

> DZNEmptyDataSet 的成功离不开其可高度定制化的模板视图.但其繁琐的 delegate apis 远不如自定义视图来的方便, 其对自定义视图的支持也并不友善.
>
> EmptyPage 优先支持 自定义视图,并附赠 3 套可以凑合看的模板视图(支持超级高自定义调节,但毕竟UI我们说了不算...)
>
> 采用 以下方式 则包含该部分内容:
>
> ```ruby
> pod 'EmptyPage'
> ```

1. ##### 自定义视图

   - **仅支持autolayout布局模式**

     > 不使用 autolayout 模式:
     >
     > 1. `pod 'EmptyPage/Core'`
     >
     > 2. `UITableView().emptyView = CustomView()`

   - **自定义视图需要autolayout实现自适应高**

     > 可以参考 内置的几套模板视图的约束实现.

   - **添加 EmptyPageContentViewProtocol 协议**

     该协议默认实现了将自定义视图居中约束至一个`backgroundView`上.

     > 通用性考虑: backgroundView.frame 与 tableView.frame 相同

     示例:

     ```swift
     class CustomView: EmptyPageContentViewProtocol{
         ...
     }
     
     let customView = CustomView()
     UITableView().emptyView = customView.mix()
     ```

     >不添加该协议,可采用以下方式:
     >
     >UITableView().emptyView = EmptyPageView.mix(view: customView)

   - **视图关系**

     ![视图关系](https://s.linhey.com/emptyPage-01.png)

2. ##### 内置模板视图

   > **特性: **
   >
   > 1. 支持链式调用.
   > 2. 元素支持高度自定义.
   > 3. 同样依照自定义视图的标准实现.
   >
   > ps: 完全等同于提前写好的自定义模板视图.

   - **目前可以选择3套基本的模板视图.**

     - 文字模板(`EmptyPageView.ContentView.onlyText`)

     - 图片模板(`EmptyPageView.ContentView.onlyImage`)

     - 混合模板(`EmptyPageView.ContentView.standard`)

     ![文字模板](https://s.linhey.com/emptyPage-03.png)![图片模板](https://s.linhey.com/emptyPage-04.png)![混合模板](https://s.linhey.com/emptyPage-02.png)

   - **使用**

     - 示例:

       ```swift
       UITableView().emptyView = EmptyPageView.ContentView.standard
       	.change(hspace: .button, value: 80)
       	.change(height: .button, value: 60)
       	.change(hspace: .image, value: 15)
       	.config(button: { (item) in
       		item.backgroundColor = UIColor.blue
       		item.contentEdgeInsets = UIEdgeInsets(top: 8, left: 20, bottom: 8, right: 20)
       	})
       	.set(image: UIImage(named: "empty-1002")!)
       	.set(title: "Connection failure", color: UIColor.black, font: UIFont.boldSystemFont(ofSize: 24))
       	.set(text: "Something has gone wrong with the internet connection. Let's give it another shot.", color: UIColor.black, font: UIFont.systemFont(ofSize: 15))
       	.set(buttonTitle: "TRY AGAIN")
       	.set(tap: {
       	// 点击事件
       	})
       	.mix()
       ```

   - **Apis**

     模板视图中总结起来只有三种配置函数:

     - 约束配置函数: `func change(...) -> Self` 

       > 约束函数具体可配置项采用枚举的形式限定.(以免改变/冲突自适应高度相关约束)
       >
       > enum HSpaceType { } // 修改视图水平方向上的间距
       >
       > enum VSpaceType { } // 修改视图垂直方向上的间距
       >
       > enum HeightType { }  // 修改视图具体高度
       >
       > 例如:
       >
       > ```swift
       > standardView.change(hspace: .button, value: 80)
       > 			.change(height: .button, value: 60)
       > ```

     - 控件配置函数: `func set(...) -> Self` 

       > 提供了简单的文本/字体/图片/颜色配置.例如:
       >
       > ```swift
       > standardView.set(title: "Connection failure", color: UIColor.black, font: UIFont.boldSystemFont(ofSize: 24))
       > ```

     - 控件自定义配置函数: `func config(element: { (element) in ... }) -> Self `

       > 返回一个完整的控件,可供深度配置. 例如:
       >
       > ```swift
       > standardView.config(button: { (item) in
       > 	item.backgroundColor = UIColor.blue
       > 	item.contentEdgeInsets = UIEdgeInsets(top: 8, left: 20, bottom: 8, right: 20)
       > 	})
       > ```

     - 视图混合函数`func mix()`:

       > 该函数由 EmptyPageContentViewProtocol 协议默认实现.
       >
       > 作用: 将视图约束至 backgroundView 上
       >
       > ps: 别忘了...

## 结尾

项目开源链接: [Github/EmptyPage](https://github.com/linhay/EmptyPage)

个人博客链接: [四方田](https://linhey.com)

