---
title: checkbox或radio中name的重要性
date: 2019-07-11 15:30:18
categories: 随笔
tags: 杂感
toc: true
---
单独的一组checkbox或者radio，一定要是单独的name，否则在设置checked效果时，将只有最后一个label（假设效果以label模拟实现）起作用。

以下是我碰到的问题：

四组input（type=radio）+label的组合，起初name全部都是sc-1-1。

![四组都设置同样的name.png](https://upload-images.jianshu.io/upload_images/29336-d53d9f4db2454e4b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看了半天css，搞不懂啥原因，后来百度才意识到有个name的问题，尝试修改为一组一个单独的name，就解决了：

![设置不同name之后.png](https://upload-images.jianshu.io/upload_images/29336-1bbd89d75af209eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

