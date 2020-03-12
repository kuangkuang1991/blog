---
title: 奇怪的img图片src修改了不刷新的问题
date: 2020-03-12 22:29
categories: 技术
tags: 技术
toc: true
---

在react（ant-design pro）项目里用Modal组件写了一个查看原图的弹窗，然后按需求又加上了左右方向键切换上一张、下一张的功能。问题出现了，监听键盘事件去修改img的src，图片压根没变化！

console一下，src变了呀！

没办法，google一通。被告知为避免缓存，需要加上随机字符。于是试试jpg后面加后缀：?+Math.random()

无效。

再试试 (new Date).getTime()。依然没反应。

好吧。我再同时改alt。问题依旧。

继续google，翻了一堆，在某github的Issues页翻到个回复

>现在要加个 key

```HTML
 <img v-lazy="img" :key="img">
```

虽然我也没用这个组件，而且我的Modal里也只有一个img，不存在一定要加key的问题。但抱着试一试的心态，加上了

>key={imgName}

再试，居然好了。莫名其妙。遂记之。