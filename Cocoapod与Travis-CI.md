---
title: Cocoapod 与 Travis-CI
date: 2018-06-30
categories: "cocoapod"
tags: 
     - Cocoapod
     - Github
     - Travis
     - CI
description: Travis-CI 是一个专门为开源项目打造的持续集成环境,与Github高度集成.支持iOS/macOS构建服务.
---

# Cocoapod 与 Travis-CI

Travis-CI 是一个专门为开源项目打造的持续集成环境,与Github高度集成.**支持iOS/macOS构建服务**.

#### 1. 使用Github账号登录[travis](https://travis-ci.org/)

   ```
   - 会自动同步gitbub下仓库目录
   ```

#### 2. 配置`.travis.yml`文件

以[Routable开源库](https://github.com/linhay/Routable)为例

默认`.travis.yml`文件:

   ```ruby
   # references:
   # * http://www.objc.io/issue-6/travis-ci.html
   # * https://github.com/supermarin/xcpretty#usage
   
   osx_image: xcode7.3
   language: objective-c
   # cache: cocoapods
   # podfile: Example/Podfile
   # before_install:
   # - gem install cocoapods # Since Travis is not always on latest version
   # - pod install --project-directory=Example
   script:
   - set -o pipefail && xcodebuild test -enableCodeCoverage YES -workspace Example/Routable.xcworkspace -scheme Routable-Example -sdk iphonesimulator9.3 ONLY_ACTIVE_ARCH=NO | xcpretty
   - pod lib lint
   ```

   `.travis.yml`修改版:

   ```ruby
   # 采用新版xcode9编译[主要是swift语法版本问题]
   osx_image: xcode9
   # objective-c/swift 都采用 objective-c 配置
   language: objective-c
   
   cache: cocoapods
   podfile: Example/Podfile
   
   env:
     global:
       - LANG=en_US.UTF-8
       - LC_ALL=en_US.UTF-8
   	# 项目路径
       - XCODE_WORKSPACE=Example/Routable.xcworkspace
     matrix:
   	# 项目名
       - SCHEME="Routable-Example"
   
   before_install:
     - gem install xcpretty --no-rdoc --no-ri --no-document --quiet
     - gem install cocoapods --pre --no-rdoc --no-ri --no-document --quiet
   	# 如果依赖其他库,最好加上
     - pod repo update
     - pod install --project-directory=Example
   
   script:
     - set -o pipefail
     - xcodebuild -workspace "$XCODE_WORKSPACE" -scheme "$SCHEME" -configuration Debug clean build CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO | xcpretty -c
     - xcodebuild -workspace "$XCODE_WORKSPACE" -scheme "$SCHEME" -configuration Release clean build CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO | xcpretty -c
     - pod lib lint --allow-warnings
   
   after_success:
     - sleep 3
   ```



#### 3. 开启CI服务   

   - 登录[travis](https://travis-ci.org/)

  ![](https://s.linhey.com/travis-ci-0.png)

   - 之后后我们 GitHub 项目 `Setting` 中的 `Integrations & services` 已经添加了 `Travis CI` 服务.

  ![](https://s.linhey.com/travis-ci-1.png)

#### 4. 触发构建服务

`Git push`会触发构建服务,同时在github上也有所指示.

  github:![](https://s.linhey.com/travis-ci-3.png)

  travis:![](https://s.linhey.com/travis-ci-2.png)



#### 5.错误日志

构建失败时,在`job log`会输出相应日志.

 

#### 6.构建徽章 Get!

![](https://s.linhey.com/travis-ci-4.png)

在项目readme.md中插入:

```markdown
[![build](https://travis-ci.org/[user]/[repo].svg?branch=master)](https://travis-ci.org/[user]/[repo])
```

🌰:

```markdown
[![build](https://travis-ci.org/linhay/Routable.svg?branch=master)](https://travis-ci.org/linhay/Routable)
```

