---
title: swift - 动态调用之performSelector
date: 2018-04-20
categories: "iOS"
tags: 
     - iOS
     - swift
description: iOS中的动态调用之performSelector.
---

# swift - 动态调用之performSelector.

### 同步执行:

```swift
public func perform(_ aSelector: Selector!) -> Unmanaged<AnyObject>!
public func perform(_ aSelector: Selector!, with object: Any!) -> Unmanaged<AnyObject>!
public func perform(_ aSelector: Selector!, with object1: Any!, with object2: Any!) -> Unmanaged<AnyObject>!
```

1. 以上函数归属于`NSObjectProtocol`.
2. 以上函数均为同步执行.
3. 以上函数与线程无关,在主线程/子线程均可直接调起指定函数.
4. `object`必须为对象类型(int,double无法正确传值).
5. 拥有返回值

### 延迟执行(Delayed perform):

```swift
open func perform(_ aSelector: Selector, with anArgument: Any?, afterDelay delay: TimeInterval, inModes modes: [RunLoopMode])
open func perform(_ aSelector: Selector, with anArgument: Any?, afterDelay delay: TimeInterval)
```

1. 以上函数归属于`NSObject`.

2. 以上函数均为异步执行.

3. 以上函数只能在主线程中执行，无法再其他线程中执行.

4. 即使delay传参为0，也不会立即执行，而是在next runloop执行

5. 有对应取消执行函数(只能在函数未到执行时间时执行有效.)

   ```swift
   open class func cancelPreviousPerformRequests(withTarget aTarget: Any, selector aSelector: Selector, object anArgument: Any?)
   open class func cancelPreviousPerformRequests(withTarget aTarget: Any)
   ```


### 线程相关:

```swift
open func performSelector(onMainThread aSelector: Selector, with arg: Any?, waitUntilDone wait: Bool, modes array: [String]?)
open func performSelector(onMainThread aSelector: Selector, with arg: Any?, waitUntilDone wait: Bool)  

open func perform(_ aSelector: Selector, on thr: Thread, with arg: Any?, waitUntilDone wait: Bool, modes array: [String]?)
open func perform(_ aSelector: Selector, on thr: Thread, with arg: Any?, waitUntilDone wait: Bool)

open func performSelector(inBackground aSelector: Selector, with arg: Any?)
```

1. 以上函数无论在主线程/子线程中执行,均会切换至指定线程中执行对应 `Selector`.
2. `waitUntilDone`:是否等待当前线程执行结束执行.

### 参数:

 1. 直接使用perform方法参数要求是`NSObject`对象,

    so: `int`,`double`之类的参数传值之后参数值一定不正确.(深坑)

 2. `String`/`Array`/`Dictionary`类型由于可以映射`NSString`/`NSArray`/`Dictionary`,并不受影响.

 3. 结构体类型可以采用`NSValue`包装的方式发送.


### 返回值

#### Unmanaged

```swift
public static func fromOpaque(_ value: UnsafeRawPointer) -> Unmanaged<Instance>
public func toOpaque() -> UnsafeMutableRawPointer
public static func passRetained(_ value: Instance) -> Unmanaged<Instance>
public static func passUnretained(_ value: Instance) -> Unmanaged<Instance>
public func takeUnretainedValue() -> Instance
public func takeRetainedValue() -> Instance
public func retain() -> Unmanaged<Instance>
public func release()
public func autorelease() -> Unmanaged<Instance>
```



参考

- [[Unmanaged](http://nshipster.cn/unmanaged/)]