---
title: leaflet地图性能优化的两个技巧
date: 2020-03-29 12:48
categories: 技术
tags: 
- 技术
- JS
- leaflet
toc: true
---

之前写了一篇 [《leaflet地图展示性能优化》](https://www.kuang2.cn/2020/03/15/leaflet%E7%82%B9%E4%BD%8D%E9%87%8D%E5%8F%A0%E6%88%96%E7%82%B9%E4%BD%8D%E8%A6%86%E7%9B%96%E9%97%AE%E9%A2%98%E7%A0%94%E7%A9%B6/)，初步研究了一下上千上万点位的渲染问题。

显而易见，这些还不够，尤其是在zoomEnd事件和moveEnd事件中写了筛选点位和线段-清空地图已有点位线段-再重新渲染点位线段的逻辑之后，缩放和移动地图时依然有明显卡顿。研究还需继续。

### 一、提高清空点位线段的效率：clearGroups 

在我们的项目中，每次缩放、移动地图都会重新进行一次筛选点位线段-清空地图上layer-重新渲染的操作。可以注意到第二步（清空地图上layer）还有明显的可优化之处。原逻辑的写法是渲染时把每个layer存入layerGroups，清空时遍历此数组，逐一清空

```JavaScript
onEachFeature: function (feature, featureLayer) {
    markerGroup.push(featureLayer);
},
……
removeGroups = groups => {
    groups && groups.forEach(function (layer) {
        // if (map && map.hasLayer(layer)) {
        if (map) {
             map.removeLayer(layer);
        }
    });
}
```

查阅官方文档，可以看到有更快速的清除方式：clearLayers

附上我的使用方式：

```JavaScript
this.markerGroups = L.layerGroup();
……
// 添加
this.markerGroups.addLayer(point).addTo(map);//L.geoJson生成point
……
// 清除
this.markerGroups && this.markerGroups.clearLayers()
```

### 二、zoomend事件和moveend事件 

虽然看上去zoomend和moveend是两个完全不同的事件，但实践告诉我们，当你缩放地图（不管是鼠标滚轮缩放还是惦记control控制条上的加号或减号），都会先触发moveend，再触发zoomend。这与我们想要的结果相违背。虽然从官方的角度来说没问题，缩放的时候确实也触发了地图的移动……

一番研究之后，最佳解决方案很快出笼。在moveend事件中加入判断即可：

```JavaScript
 if (map.getZoom() != this.state.curZoom) {
    // 此时是缩放引发的move。不必处理
    return;
 }
```

如果触发moveend时，当前缩放层级zoom与存储于state的zoom层级不同，即代表是缩放触发的moveend，可以跳过
