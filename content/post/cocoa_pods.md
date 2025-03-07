
---
title: "cocoa pods"

date: "2016-01-12 10:29:32"

tags: 
    - iOS
---

* CocoaPods 是 iOS 最常用最有名的类库管理工具
* 作为 iOS 程序员, 掌握 CocoaPods 的使用是必不可少的基本技能

##pod 命令汇总
```
# 第一次使用安装框架
$ pod install

# 今后升级、添加、删除框架，或者框架不好用
$ pod update

# 搜索框架
$ pod search XXX

# 在项目文件夹下创建默认的 Podfile
$ pod init
```

<!-- more --> 

##Pod file 格式说明

```
# 最低支持的 iOS 版本
platform :ios, '8.0'

# Swift 项目需要将框架转换为 frameworks 才能使用
use_frameworks!

# 框架列表
pod 'AFNetworking'
```

##Pod 安装
```
# 添加源
$ sudo gem sources -a https://ruby.taobao.org/
# 删除源
$ sudo gem sources -r https://rubygems.org/
# 安装
$ sudo gem install cocoapods
# 设置
$ pod setup
```
##gem 常用命令
```
# 查看gem源
$ gem sources –l
# gem自身升级
$ sudo gem update --system
# 查看版本
$ gem --version
# 清除过期的gem
$ sudo gem cleanup
# 安装包
$ sudo gem install cocoapods
# 删除包
$ gem uninstall cocoapods
# 更新包
$ sudo gem update
# 列出本地安装的包
$ gem list
```