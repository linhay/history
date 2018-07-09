---
title: Swift-计算函数运行时间
date: 2018-04-08 
categories: "ios"
tags: 
     - ios
     - swift
---

### Date

```swift
  let t0 = Date()
  sleep(2)
  let t1 = Date()
  print(t1.timeIntervalSince(t0)  * 1000 * 1000 * 1000)
```

### CFDate

```swift
  let t0 = CFAbsoluteTimeGetCurrent()
  sleep(2)
  let t1 = CFAbsoluteTimeGetCurrent()
  print((t1 - t0) * 1000 * 1000 * 1000)
```

### ProcessInfo

```swift
  let t0 = ProcessInfo.processInfo.systemUptime
  sleep(2)
  let t1 = ProcessInfo.processInfo.systemUptime
  print((t1 - t0) * 1000 * 1000 * 1000)
```

<!--more-->

### Mach 

1. 时间例程依赖于所需要测量的时间域.
2. 某些情况下使用诸如clock()或getrusage()函数来做些简单的数学运算就足够了.

####mach_absolute_time

1. mach_absolute_time是一个CPU/总线依赖函数，返回一个基于系统启动后的时钟”嘀嗒”数.
2. mach_absolute_time可以获得纳秒级精度的时间.

```
// 获取转换因子
var info = mach_timebase_info_data_t()
mach_timebase_info(&info)
// 获取开始时间
let t0 = mach_absolute_time()
sleep(2)
// 获取结束时间
let t1 = mach_absolute_time()
print(UInt32(t1 - t0) * info.numer / info.denom)
```

3. [Apple 官方对某个开发者的提问What time units does it use?进行的回复](https://developer.apple.com/library/content/qa/qa1398/_index.html)