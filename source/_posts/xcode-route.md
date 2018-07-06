---
title: Node.js 安装依赖编译 C++ node-gyp 报错 unknown type name 
date: 2018-06-01 11:32:46
tags: [ 'C++', 'Node.js' ]
---

前段时间更新 Mac OS 的系统，也同样更新了 Xcode。在公司的项目中，用了死月大佬封装 C++ ons 版本得到的 [Aliyun ons](https://github.com/XadillaX/aliyun-ons)。在重新安装依赖时，发现在编译 ons 的时候不停爆出`unknown type name 'uint32_t'`这样的错误。



本以为是 npm 的缓存、c++ 版本、node 版本等原因，当我用另一台 Mac 安装 ons 并成功之后，完全不知道该怎么做。这时碰巧切了一个新的 Node 版本，在编译时报出了一个 Warn: `xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance`。



查询网上资料说 Xcode 的开发路径被错误更改了。



执行命令: 

```sh
sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer
```

再重新编译安装 C++ 就没有这个问题了。



原因估计是 C++ 库版本的问题。之前路径的库可能是系统自带的，相对版本会老一点。
