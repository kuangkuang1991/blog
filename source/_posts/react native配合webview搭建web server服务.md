---
title: react native配合webview搭建web server服务
date: 2023-12-23 17:22
categories: 技术
tags: 
- 技术
- JS
- react native
- web server
- webview
toc: true
---


通常如果我们想在react native项目中使用h5，会考虑使用webview引用本地assets文件夹中的html静态文件。但这样的使用方式限制很大，比如无法使用http下才能使用的一些方法，亦或者是被cross origin这样的跨域错误折磨得没脾气。

这个时候我们不得不考虑将web挂载到远程的网站上，直接让webview指向网站地址。但这样又会明显增加维护复杂度，同时用户体验也会极度依赖于网络的稳定和网速。

不妨考虑另一种思路，在本地host一个web server，就像服务器干的事情一样。刚好有一个能堪当此重任的库：react-native-http-bridge-refurbished

用法也很简单：

````JavaScript

import {BridgeServer} from 'react-native-http-bridge-refurbished';

 useEffect(() => {
        const server = new BridgeServer('http_service', true);
        server.get('/', async (req, res) => {
            // do something
            return {message: 'OK'}; // or res.json({message: 'OK'});
        });
        server.listen(3000);

        return () => {
            server.stop();
        };
    }, []);

````

本仓库的官方例子过于简单，还无法基于此建立我们想要的web server。还需要引入react-native-fs 来读取html等文件。

使用readFileAssets读取assets文件夹下的html文件，同时判断请求的res的类型来指定不同的header type，即可完整处理html关联文件。

````JavaScript

const { url } = req;
let filePath="htmls/badge.html", type = 'text/html';
if (!url.endsWith('html')) {
    filePath = "htmls" + url;
}
if (url.endsWith('css')) {
  type = 'text/css';
}
if (url.endsWith('js')) {
  type = 'text/javascript';
}
if (url.endsWith('png')) {
  type = 'image/png';
}
const format = url.endsWith('png') ? 'base64' : 'utf8';
let file= await RNFS.readFileAssets(filePath, format);
return  res.send(200, type, file);

````
