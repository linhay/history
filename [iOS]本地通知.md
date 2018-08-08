---
title: iOS-本地通知
date: 2018-04-08 
categories: [ios]
tags: [ios,Notification]
---

# iOS-本地通知

## 一 . UILocalNotification 

#### 0. Api支持

   ```swift
@available(iOS, introduced: 4.0, deprecated: 10.0, message: "Use UserNotifications Framework's UNNotificationRequest")
   ```

#### 1. 注册通知

   ```swift
let settings = UIUserNotificationSettings(types: [.alert,.badge,.sound], categories: nil)
UIApplication.shared.registerUserNotificationSettings(settings)
   ```

#### 2. 发送通知

```swift
 // 创建本地通知
 let note = UILocalNotification()
 // 设置通知发送的时间
 note.fireDate = date
 // 标题
 note.alertTitle = "通知标题"
 // 设置通知内容
 note.alertBody = "通知内容"
 // 设置通过到来的声音
 note.soundName = UILocalNotificationDefaultSoundName
 // 设置应用图标左上角显示的数字
 note.applicationIconBadgeNumber = 2
 // 设置一些额外的信息
 note.userInfo = ["key":"value"]
 // 执行通知
 UIApplication.shared.scheduleLocalNotification(note)
```

#### 3. 通知接收

   ```swift
/// app处于前台 / app处于后台时
func application(_ application: UIApplication, didReceive notification: UILocalNotification) {
/// doSomeThing  
}
   ```

## 二 . UserNotifications  

#### 0. Api支持:

```swift
@available(iOS 10.0, *)
```

#### 1. 注册通知

   ```swift
let center = UNUserNotificationCenter.current()
/// 需要响应事件或需在前台时显示通知 需要实现该代理
center.delegate = self as UNUserNotificationCenterDelegate
/// 注册通知响应类型
center.requestAuthorization(options: [.alert,.badge,.carPlay,.sound]) { (res, err) in
   	if res { print(err) }
}
   ```

#### 2. 发送通知

   - 构建内容: [UNMutableNotificationContent](https://developer.apple.com/documentation/usernotifications/unmutablenotificationcontent)

     ```swift
     let content = UNMutableNotificationContent()
     content.badge = 55
     content.body = "body"
     content.title = "title"
     content.subtitle = "subtitle"
     content.userInfo = ["key": "自定义参数"]
     content.launchImageName = "logo.png"
     ```

   - 构建触发器: `UNNotificationTrigger`

     - 时间触发器: [UNTimeIntervalNotificationTrigger](https://developer.apple.com/documentation/usernotifications/UNTimeIntervalNotificationTrigger)

       ```swift
       // 5s 后触发
       let trigger = UNTimeIntervalNotificationTrigger(timeInterval: 5, repeats: false)
       // 每隔 60s 重复触发 (重复触发要求时间间隔大于 60s)
       let trigger = UNTimeIntervalNotificationTrigger(timeInterval: 60, repeats: true)
       ```

     - 日历触发器: [UNCalendarNotificationTrigger ](https://developer.apple.com/documentation/usernotifications/uncalendarnotificationtrigger)

       ```swift
       //每周一早上 8：00 触发 (周日为一周开始)
       var components = DateComponents()
       components.weekday = 2
       components.hour = 8
       trigger = UNCalendarNotificationTrigger(dateMatching: components, repeats: true)
       
       /* 扩展:
       DateComponents:  一星期: [第一天为星期天]，[最后一天为星期六]
       [ISO 8601]: 一星期: [第一天为星期一]，[最后一天为星期天]
       url: https://www.iso.org/iso-8601-date-and-time-format.html
       8/
       ```

     - 地理围栏触发器: [UNLocationNotificationTrigger](https://developer.apple.com/documentation/usernotifications/UNLocationNotificationTrigger)

       1. 需导入定位库` import CoreLocation `,
       2. CLRegion 有 2个子类分别为
          - [CLCircularRegion](https://developer.apple.com/documentation/corelocation/clcircularregion): 经纬度-地理围栏
          - [CLBeaconRegion](https://developer.apple.com/documentation/corelocation/clbeaconregion): iBeacon设备-地理围栏

       ```swift
       // 1. 经纬度 地理围栏
       //距离(30, 30）点 20 米 处发起通知
       let coor = CLLocationCoordinate2D(latitude: 30.00, longitude: 30.00)
       var region = CLCircularRegion(center: coor,radius: 20,identifier: "regionidentifier")
       // 退出围栏时触发
       region.notifyOnExit = true
       // 进入围栏时触发
       region.notifyOnEntry = true
       trigger = UNLocationNotificationTrigger(region: region, repeats: isRepeats)
       
       // 2. iBeacon
       // 还在探索...
       ```

     - 发送通知

       ```swift
       let req = UNNotificationRequest(identifier: "identifier", content: content, trigger: trigger)
       UNUserNotificationCenter.current().add(req, withCompletionHandler: nil)
       ```

#### 3. 通知接收

```swift
 // 处理通知激活事件
 func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
     completionHandler()
 }
   
 // 如果需要在应用内展示通知则可实现该代理函数
 func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
     completionHandler([.alert,.badge,.sound])
 }
```


​     

