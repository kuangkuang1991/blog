---
title: react的父子组件执行时间顺序
date: 2020-11-13 22:48
categories: 技术
tags: 
- 技术
- JS
- react
toc: true
---

最近做项目遇到些坑，简单点说就是react项目里写在组件文件头部（import之后，class声明之前）的逻辑，或者是constuctor函数里面的逻辑————以读取localStorage数据为主———没有按想象的执行。

于是今天写了几个console来看看组件中代码的执行时间顺序到底是怎么样的，见图。

<p style="text-align:center;"><img src="/images/父子组件的执行时间顺序.png" style="width:60%" alt="父子组件的执行时间顺序"/></p>

通常react教程文章会忽视前面两个。只会告诉你“父constructor-子constructor-子compenentDidMount-父componentDidMount”这样的顺序，而忘了子组件文件的读取在父组件文件之前。

当然，在class声明之前写逻辑的操作不太对。