---
title: leaflet点位重叠/点位覆盖/点位遮盖问题研究
date: 2020-03-15 12:48
categories: 技术
tags: 
- 技术
- JS
- leaflet
toc: true
---

随着leaflet学习的深入，新的问题出现在眼前：

如果两个点位经纬度完全相同，怎么办？

<p style="text-align:center;"><img src="/images/点位重叠点位覆盖问题.png" style="width:30%" title="" alt=""/></p>

如图所示，实际上就是两个点位。只是点位A的图标把点位B的图标遮盖住了，而名字却不一样，导致文字辨认不清。

外事不决问谷歌。在老地方csdn找到一篇[博客](https://blog.csdn.net/u013517229/article/details/79311635)提到可以使用setZIndexOffset 方法手动提升marker的显示层级。但……没说具体怎么用。方法是好方法，但作者你也过于言简意赅了吧。

又硬着头皮翻leaflet文档。找到两处相关论述。

一个是point下提供了equals方法。可以判断两个点位的经纬度是否完全一致。不错，留着

<p style="text-align:center;"><img src="/images/点位equals.png" width="50%"  title="" alt=""/></p>

另一个是remove方法。可以移除单个marker。由此引发的思路是，给marker绑定一个右键点击事件，鼠标点击即remove掉marker。因为我们的地图在缩放和移动时都会重新遍历markerList再渲染，所以等于是临时隐藏了。隐藏了上面一层的点位，下面的自然就显示出来了。

这个方案简单粗暴，不禁为自己鼓掌30分钟……

然而程序毕竟是给人用的，用户友好度要考虑。用户鬼知道你这里右键会隐藏点位啊。搞不好还以为bug了。为这么个小功能，还得专门在培训手册里加一条，不值当。

考虑体验的话，一般会提供一个选择弹窗，也就是说，正常思路下的解决方案应该是，用户在点击该点位想查看详情的时候，弹个窗口问他到底想看哪一个。

leaflet有跟随鼠标的小窗口吗，除了tooltips。还真有。popup。

本来官方插件库里有一个专门的右键菜单插件提供：[Leaflet.contextmenu](https://github.com/aratcliffe/Leaflet.contextmenu)

但想到又要在已经臃肿不堪的项目里再加一个插件就头痛，我需要不就是个弹窗嘛。自己写！

popup已经挺好了，再结合L.DomUtil来创建dom元素，基本就够了。

参考这篇文章，自建右键菜单:[将处理程序添加到弹出传单](https://stackoom.com/question/3fKcH/%E5%B0%86%E5%A4%84%E7%90%86%E7%A8%8B%E5%BA%8F%E6%B7%BB%E5%8A%A0%E5%88%B0%E5%BC%B9%E5%87%BA%E4%BC%A0%E5%8D%95)

核心代码：

```JavaScript
var popup = L.popup();
  var container = L.DomUtil.create('div');
var btn = L.DomUtil.create('button', '', container);
  btn.setAttribute('type', 'button');
 btn.innerHTML = 'hello kees';
  L.DomEvent.on(btn, 'click', () => {
   alert("hello kees");
  });
  ...
 popup.setLatLng(e.latlng)
    .setContent(container)
   .openOn(mymap);
```

在marker的鼠标悬浮事件上增加选点弹窗即可：

<p style="text-align:center;"><img src="/images/选点位.png" style="width:30%"  title="" alt=""/></p>

此外，leaflet plugins也提供了类似问题的处理方式，如 [OverlappingMarkerSpiderfier](https://github.com/jawj/OverlappingMarkerSpiderfier-Leaflet)，[demo](http://jawj.github.io/OverlappingMarkerSpiderfier-Leaflet/demo.html)


