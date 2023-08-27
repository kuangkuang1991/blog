---
title: react native如何实现3D效果及视频导出？
date: 2023-07-07 22:00
categories: 技术
tags: 
- 技术
- JS
- react native
- threejs
toc: true
---


对于3D实现，当然少不了threejs。再加上react，那一定首推react-three-fiber。刚好react-three-fiber也是[支持native使用的](https://docs.pmnd.rs/react-three-fiber/getting-started/installation#react-native)。

然而经过实测，react-three-fiber能实现原生native的3d效果是没错，但加载gltf却是个大坑，至今有issues没解决。（官网文档写到React Native的部分，也只有简单的mesh案例）于是放弃，还是得像在RN中加载map一样，老老实实考虑webview的方案。

虽说在RN中用webview看上去有点偷懒，但实话说有些技术还真就是web更好使。包括且不限于gis，xr/vr，crypto等。如果体验和性能上不构成阻碍，也不妨用一用webview这个万金油。

当当当……（此处省略千字）关于如何使用web（react-three-fiber）调用加载gltf的3d文件，网上教程就太多了。此处继续讨论核心问题。

首当其冲的是，RN加载webview的理想形式，当然是在本地文件夹中load一个静态的html。毕竟挂url网站一提高了维护成本，二也因为可能的网络原因影响了性能。几番搜索之下，找到了[react-snap](https://github.com/stereobooster/react-snap)这个库。简单配置即可将常常需要web服务器支撑的react应用build成静态html。

然而须知静态html的限制还是蛮大的，果然刚喜滋滋的将打包好的html打开，就发现控制台报错资源跨域无法加载。包括gltf格式的3d文件，以及text3d使用的font文件（json格式）。又是几番尝试下，发现远程url加载是支持的。于是这个问题跳过了。

效果有了。进入我们议题的第二步：视频的生成和导出。

最初的设想是回到RN的领域解决。也确实发现了一个看上去似乎能满足需求的库： [react-native-record-screen](https://github.com/yutasuzuki/react-native-record-screen)。然而加载进去死活跑不起来，报kotlin相关错误，无奈不是android高手，这个方案也搁置了。

既然本来就是web生成的页面，咱就继续考虑web方案吧。

首先想到的是webrtc。然后过于打断交互的确认框实在不适合我们项目的使用场景，放弃。（[webRTC体验在此](https://www.webrtc-experiment.com/getDisplayMedia/)）

然后又是几番搜索，得知canvas本身就是支持流输出的：canvas.captureStream(60)。传参即帧率。然后可以实例化MediaRecorder来捕获生成视频流，完了再new一个blob传入URL.revokeObjectURL即可下载了。[这篇文章写的很好。](https://julien-decharentenay.medium.com/how-to-save-html-canvas-animation-as-a-video-421157c2203b)

简单贴一下代码：

````
// 获取视频流
var chunks = [];
var canvas_stream = canvas.captureStream(30); // fps
// Create media recorder from canvas stream
this.media_recorder = new MediaRecorder(canvas_stream, { mimeType: "video/webm; codecs=vp9" });
// Record data in chunks array when data is available
this.media_recorder.ondataavailable = (evt) => { chunks.push(evt.data); };
// Provide recorded data when recording stops
this.media_recorder.onstop = () => {this.on_media_recorder_stop(chunks);}
// Start recording using a 1s timeslice [ie data is made available every 1s)
this.media_recorder.start(1000);

// 建立下载
// Gather chunks of video data into a blob and create an object URL
var blob = new Blob(chunks, {type: "video/webm" });
const recording_url = URL.createObjectURL(blob);
// Attach the object URL to an <a> element, setting the download file name
const a = document.createElement('a');
a.style = "display: none;";
a.href = recording_url;
a.download = "video.webm";
document.body.appendChild(a);
// Trigger the file download
a.click();

````

然而最大的坑出现了。MediaRecorder只能支持webm和x-matroska格式（见），离用户可以直接预览打开的mp4还很远。

至此进入到视频转格式领域。果然又绕不开ffmeng了。ffmeng倒也有web的实现方案。然而一通操作下来，发现最核心的转格式方法，需要浏览器的多进程相关api——SharedArrayBuffer。而此api在早期的浏览器版本中就被限制了。（[见此问题: SharedArrayBuffer is not defined ](issue：https://github.com/ffmpegwasm/ffmpeg.wasm/issues/263)）

基于issue的翻阅和第N回的疯狂搜索，又让我找到了个好东西：[coi-serviceworker](https://github.com/gzuidhof/coi-serviceworker)。不改服务器配置即可享受安全限制的豁免，实现上述功能。可惜还是反过来限制了咱们想用webview加载静态html的初衷。但也非常满足了。

科学研究有云，低期望乃幸福本源。能做到如此程度，咱就偷着乐吧。