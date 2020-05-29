---
title: ant-design-pro的坑与使用心得
date: 2020-05-30 13:48
categories: 技术
tags: 
- 技术
- JS
- ant-design-pro
toc: true
---
首先是两道开胃菜。在升级V4之后碰到的问题

### select值为null或者‘’时，不显示placeholder

解答是null或者‘’被当做正常值了

详见：[Select 控件为什么会把 null 当做有 value 而不显示 placeholder ，必须要为 undefined 才可以](https://github.com/ant-design/ant-design/issues/2367)


### form第一次render后，initvalue值改变无效

解答是initialValue 和 defaultValue 一样，只有初始化时生效。

[form 表单中 initialValue只能显示第一次传进来的值，之后传入的值都无法显示](https://github.com/ant-design/ant-design/issues/5109)


### 生产环境去除console信息

这个也是挺常用的需求，但是答案却没见官方文档啥的，还是得自己google翻一圈。

首先是使用babel-plugin-transform-remove-console这个插件。

然后在config文件夹的config.ts文件中，导出obj里写入：

```JavaScript
extraBabelPlugins:[IS_PROD?'transform-remove-console':'']

```
umi 3配置bable插件的说明也是一言难尽。。。


### effect的loading在请求报错后不结束

又是一个大坑，在这个问题耗费的时间是最长的。在Issues页面也是N个loading相关页，没几个把话说清楚的。最后终于给我翻到答案：

[请求发生错误时怎么取消loading？](https://github.com/dvajs/dva/issues/1580)


```JavaScript
export const dva = {
  config: {
    onError(e) {
      e.preventDefault();
      console.error(e.message);
    },
  },
};
```


再附送两个业务需求催生的心得：

### 跳转页面前判断

本来以为是在路由监听里处理，也就是models文件夹下global.ts文件的 subscriptions-setup对象内做history.listen事件监听。后来发现业务需求是跳转前的拦截。

一通研究，才发现答案简单得不能再简单。直接在layouts文件夹下BasicLayout文件中重写menuItemRender方法，给菜单a标签绑定click事件，在事件中判断即可。。。


### 登录后跳转到登录前页面

这个问题刚开始也是在Issues页一通搜索来着，然后看到开发大大轻飘飘一句：sessionStorage实现即可……

我本来写的是history.goBack 。后来发现如果是500页退出到登录页，登录再跳回500页也是谜一样的操作了。故还是改为sessionStorage