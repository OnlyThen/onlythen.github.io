---
layout:     post
title:      "系统环境备忘"
subtitle:   "我的电脑有什么"
date:       2017-10-24
author:     "XiaoYu"
header-img: "img/post-system-environment.jpg"
catalog:    true
tags:
    - Code
    - macOS
---

> 最近开始尝试了人脸识别的`demo`，用了一些`module`，又在电脑上配置了很多环境，遇到很多`error`，想着做个备忘，以后好参考。

## 基本命令

这些都是`bash`里常用的命令，最容易忘记的就是进入外挂存储了。
```
cd /Volumes/MYLOVE
```
还有C程序的编译及调试，涉及到`GDB`，这都可以随用随查，我个人还是不喜欢命令行操作，有时方便，有时又不直观。

## Libpcap

`Libpcap`在`Ubuntu`下早已用过，`macOS`基于`UNIX`，自然也是可以使用的，像这些包都可以直接下载下来 然后进入目录`Compile`就可以了。

编译程序时加上`-lpcap`就可以了。

## Nessus

这个漏洞扫描软件还是信息系统安全课程时使用的，太久没有，已经忘了怎么安装的了。不过参考网站上的文档应该很简单，我也下载过帮助文档，

## Python

`python`到现在还没学会，要好好下功夫了，毕竟这个语言还是十分重要的，`macOS`自带了`python2.7`，我又下载了`python3.6`，实在不明白弄两个版本是怎么回事，如果互相不兼容就直接放弃一个就好。

## Xcode

**Xcode**是我用的最多的`IDE`了，iOS开发是少不了它的，一般我写C和C++程序也用的它，只要顺手就好，Xcode自带了Command Line Tools，但是我在Bash里又下载了一个。

## Homebrew

`macOS`缺失的软件包管理器，确实名不虚传，官网上有详细的介绍，使用起来也很方便。

## opencv

最近接触人脸识别时知道了这个库，是用C/C++写的，移植性很好，本来不打算使用，结果后来再Github上看到一个项目——[face_recognition](https://github.com/ageitgey/face_recognition)，使用了这个库，结果花了我一天的时间安装这两个东西，还是把安装过程记录下：
安装`dlib`
```js
brew install cmake
brew install boost
brew install boost-python --with-python3
pip3 install dlib
```

安装`face_recognition`
```js
pip3 install face_recognition
```

安装`opencv`
```js
brew install opencv3 --with-python3 --c++11 --with-contrib
brew link --force opencv3
```

我这边的问题是之前安装过`opencv`，它和我系统的python2.7软链接了，所以我又在命令行进入python3.6.1（十分麻烦），手动链接成功，最后当电脑可以跑起来时，所有的努力还是值得的。

## 最后

Mac在软件开发上相比于Windows具有天然的优势，明年应该还会购买一台iMac。



