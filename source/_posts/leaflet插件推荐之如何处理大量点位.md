---
title: leaflet插件推荐之如何处理大量点位
date: 2021-07-13 21:48
categories: 技术
tags: 
- 技术
- JS
- leaflet
toc: true
---

### 1.[Leaflet.markercluster](https://github.com/Leaflet/Leaflet.markercluster) 点位聚合

聚合插件

[demo](https://leaflet.github.io/Leaflet.markercluster/example/marker-clustering-realworld.388.html)


### 2.[Leaflet.PixiOverlay](https://github.com/manubb/Leaflet.PixiOverlay)

使用Pixi.js绘制叠加层，Pixi.js是一个使用 WebGL 绘制的 JavaScript 库

[demo](https://manubb.github.io/Leaflet.PixiOverlay/french-cities.html)


### 3.[leaflet-markers-canvas](https://github.com/lipton-ice-tea/leaflet-canvas-markers)

用法：

````javascript

require('./leaflet-markers-canvas.min.js');

markersCanvas = new L.MarkersCanvas();

markersCanvas.addTo(map);

markersCanvas.addMarker(marker, null);


````

### 4.[leaflet.canvas-markers](https://github.com/corg/Leaflet.Canvas-Markers)

用法：

````javascript

markersCanvas = L.canvasIconLayer({}).addTo(map);

markersCanvas.addOnClickListener(function(e, arr){
  const data = arr[0].data;
})

ciLayerref.current.addLayers(markers);


````

使用插件leaflet.canvas-markers在缩放时，图标的缩放会产生延迟，使用[fork的分支](https://github.com/corg/Leaflet.Canvas-Markers)


### 5.canvas marker如何使用自定义颜色的图标？

之前项目的一个核心需求就是可以自定义图标颜色，然而canvas marker类插件只支持固定的图片图标。难道要制作N个不同颜色的图标文件吗？

百般研究之后，我的解决方案是（把原来的SVG图标文件）————转成cavans函数绘制，然后颜色值作为fillcolor传参

使用 [svg2canvas](http://demo.qunee.com/svg2canvas/) 工具即可