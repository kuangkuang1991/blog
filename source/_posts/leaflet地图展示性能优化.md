---
title: leaflet地图展示性能优化
date: 2020-03-01 12:48
categories: 技术
tags: 
- 技术
- JS
- leaflet
toc: true
---

最近项目有个需求，要同时展示几千甚至上万个点位和线段。调用数据一看，浏览器直接卡死。

没办法，只得马上上网搜解决方案。leaflet相关页面挺多，但大多都是基础教程，api翻译也都力不从心似的，错别字一堆。没办法，继续挠头看文档。

首先考虑是不是有相关插件的解决方案。

果然，在plugins页面，就有一个板块，Clustering/Decluttering，翻译过来就是聚合、整理。

<p style="text-align:center;"><img src="/images/1583043716365.png" width="50%" height="50%" title="" alt=""/></p>

比如Leaflet.markercluster。点进去一看，demo图的效果一目了然。

<p style="text-align:center;"><img src="/images/1583043748468.png" width="50%" height="50%" title="" alt=""/></p>

试了一下，效果确实不错。

但还是感觉不够彻底。因为我们的数据是geojson格式的。所有点位数据都要走pointToLayer方法。在控制台打日志，可以看到如果有上万个点位，每一条数据都要跑一遍pointToLayer方法，虽然一遍也就几十毫秒，然而累加起来的话，浏览器基本还是半天不动的状态。

能不能直接减少数据呢？通过筛减点位的方式？

后台同事找到了一种压缩算法：[矢量压缩算法-光栏法][https://blog.csdn.net/weixin_30342209/article/details/99275544]

大概意思就是在一条线上的密集点位进行抽稀。

（后来也看到了一个JS抽稀的例子：[基于Geojson的点集的抽稀Js实现][]）

这样缩减之后，前端得到的数据量确实大大减少。然而问题还没结束。概化的显示只能在比较小的缩放层级上，大的层级，缩放到最大时，还是得老老实实把应该有的数据展示出来。这样的话，后台能做的确实有限。

努力翻文档的同事终于找到个真正给力的方法：map.getBounds().contains 。这个方法能判断点位是否在指定区域内。也就是说，我们可以根据当前显示的区域，在缩放和移动时过滤一遍所有点位，只把当前区域范围内的点位筛选出来，进入geojson的pointToLayer，渲染出来。因为筛选的过程只是逻辑计算，不会有太大的性能消耗。

这样一来，展示性能确实有极大的提升。

但是问题还没结束。点位过滤了。线段呢？

因为这个问题是派到我头上，所以我成了新的翻文档挠头人。

在起初，似乎并没有找到类似点位这样可以直接判断线段在不在范围内的方法，我想到的方法是把线段均分成点，比如计算出一条线上的五个等分点。然后循环跑一遍点位的判断。

这样的问题是不够严密。如果线太长，只能加大等分密度：10等分点、100等分点……然而你无法估计线的长度。

有彻底的方法吗？

再翻一遍文档，果然还是有的。和点位的判断类似，map.getBounds()下还有一个intersects方法。判断可视区域是否与指定的区域相交。如果把线段看作区域，确实可以用这个方法。

但是还需要做一层处理。这次就只能翻源码了。intersects的参数是一个bounds类型的对象。而geometryToLayer可以把线段转换为区域。于是：

>let bounds = map.getBounds()
>
>var layer = L.GeoJSON.geometryToLayer(line, {}); //line是单个线段
>
>​let areaContainLine = bounds.intersects(layer.getBounds())

这样终于能调用成功了。效果也确实让人满意。判断非常准确。

到此，故事还没有完全结束。考虑到项目后期的需求，如果经常是几万个点位的同时展示，性能依然还是够呛。有更深层的解决方案吗？

答案依然是——有的！

Leaflet.Canvas-Markers。这个插件就静静躺在plugins页面的marker部分下。插件对marker的处理是颠覆性的——直接转为canvas渲染，这样dom操作就直接免掉了。

<p style="text-align:center;"><img src="/images/1583050434859.png" width="50%" height="50%" title="" alt=""/></p>

看demo：<https://ejuke.github.io/Leaflet.Canvas-Markers/examples/index.html>

然而遗憾的是它使用的是原生的L.icon，这样就无法像使用divIcon那样去自定义图标的颜色。只能当做某种性能模式的选择项。