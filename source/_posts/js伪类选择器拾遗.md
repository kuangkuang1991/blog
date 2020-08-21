---
title: js伪类选择器拾遗
date: 2020-08-21 21:40
categories: 技术
tags: 
- 技术
- JS
- JS基础
toc: true
---

每次想用css3的伪类选择器去选类似于第X个p元素或者列表最后一个li时，都会记忆错乱，面露难色，然后红着脸点开浏览器书签里的“css选择器参考手册”。

互联网是真好用，用了是真的离不开。但几年过去，在这方面似乎还是毫无长进。受不鸟了。赶紧拾掇一篇加强记忆吧。

## 选择首尾元素

:first-of-type、:first-child、:last-of-type、:last-child

<p style="text-align:center;"><img src="/images/first-of-type.png" style="width:65%"/></p>

<p style="text-align:center;"><img src="/images/last-of-type.png" style="width:65%"/></p>

关于后面两者的区别，这篇讲的很清楚：[:last-child与:last-of-type的区别](https://www.jianshu.com/p/2403914c7755)

简单来说，就是p:last-child是两项限制，最后一个元素必须是p元素，才能生效。p:last-of-type则只看是否是最后一个p元素


## 选择唯一的元素

:only-of-type、:only-child

区别同上，对于p:only-of-type和p:only-child而言，当有其它干扰元素时，后者不会生效


## 按顺序值选择元素

按正序选择:nth-child(n)、:nth-of-type(n)

按倒序选择:nth-last-child(n)、:nth-last-of-type(n)  


## 限制元素

这一条之前还没意识到，直到记录下这条笔记。就是顺序选择器前的空格。当有空格时，实际上空格前的元素就变成了父元素

<p style="text-align:center;"><img src="/images/css选择器-无空格.png" style="width:65%"/></p>

<p style="text-align:center;"><img src="/images/css选择器-有空格.png" style="width:65%"/></p>

其实等价于空格是用于子元素指定类型的。换一个说法就是，在css3的选择器逻辑里，元素空格后面本身就是子元素的选择。

最后一个截图的例子也提醒我们，如果没有限定子元素，of-type类型的选择器，是不区分类型的同时还不限定数量，每种类型都匹配一个。这也就是-child类选择器同时存在的原因吧。


## 配合:not(selector) 实现反向选择

<p style="text-align:center;"><img src="/images/css选择器-反向选择.png" style="width:65%"/></p>


## :nth-of-type()里填正数以外的值：关键字、公式

关键字有odd（奇数），even（偶数）。这两个单词也好记，奇数（3）个字母的odd就是奇数，偶数（4）个字母的even就是偶数.

公式即（an+ b）：a代表一个循环的大小，N是一个计数器（从0开始），以及b是偏移量

比如:nth-of-type(2n)，即偶数个子元素，:nth-of-type(2n+1)，奇数个子元素，(-n+3)，n指循环，即把0-3带入n得到的子元素集合：3、2、1、0

<p style="text-align:center;"><img src="/images/css选择器-n的公式.png" style="width:65%"/></p>

本文也参考了[21条CSS高级技巧](http://caibaojian.com/css-protips.html)


