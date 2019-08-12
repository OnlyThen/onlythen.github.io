---
layout:     post
title:      "第一次Pull-request"
subtitle:   "代码笔记第九话"
date:       2019-08-10
author:     "XiaoYu"
header-img: "img/post-git.png"
tags:
    - Github
    - OpenVPN
    - Code
---

> 最近在在研究`iOS`平台下的`OpenVPN`客户端，找到开源的`Passepartout`，我对它进行了简体中文翻译。

## 此次的工作

接触`Git`与`GitHub`也有很多年了，两者的介绍网上也有很多，最主要的是自己多去用，自然就会熟悉并理解。最近在`Github`上进行了第一次提交，其实还是比较简单的，像这种开源项目的参与，项目越大，管理的重要性也就越重要，我这次做的是翻译工作，没有什么难度，算是体验一下`pull request`的内容。
![request](/img/in_post/request.png)
---

## Git

听说`Git`这个东西是业界大神Linus Benedict Torvalds花了一个礼拜写出来管理Linux内核开发的工具，不管他花的时间的多少，都不影响他的重要性。这一工具确实极大方便了开发者的协作以及管理。我也对此工具的实现产生了兴趣。

Git作为一个分布式版本控制系统，那么最关键的两个功能便是分布以及版本管理，它实现了创建分支以及合并、分布式存储、数据的保证、缓存区等功能，最重要的是它可以在多个分支，多人协作的情况下保证代码库的版本稳定，那么它是怎么做到的呢？Git的源代码可以在这里找到。[Git](https://github.com/git/git)

### git clone

我们使用该命令来克隆远程的仓库到本地，



