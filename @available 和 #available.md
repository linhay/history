---
title: (施工ing) swift中的 @available
date: 2018-04-20
categories: "iOS"
tags: 
     - siwft
     - ios
description: iOS中的api限制,与api限制判断.
---

# @available 和 #available

```
  @available(swift,
  			introduced: 指定版本,
  			deprecated: 从指定平台某个版本开始过期该声明, 
  			obsoleted: 从指定平台某个版本开始废弃该声明,
            message: 附加信息,
            unavailable: 指定无效平台,
            renamed: 对应新的声明名)
```

