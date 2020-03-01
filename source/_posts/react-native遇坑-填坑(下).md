---
title: react-native遇坑-填坑(下)
date: 2019-05-05 12:48
categories: 技术
tags: 
- 技术
- JS
- reactnatie
toc: true
---

#### 1.modal弹窗在页面刷新时，页面不可操作、显示不正常（未解决）

使用antd的modal弹窗组件，在页面刷新时，可能保持刷新前的状态（展开或关闭），且造成页面的不可点击或其他操作

如果是展开状态，并且不可见，需要点击关闭（估摸关闭按钮的位置），才能继续操作界面

注：webview的父组件view需要设置高度

#### 2.FlatList报错 Tried to get frame for out of range index NaN

原因是FlatList的data数据源不是标准的数组对象格式

#### 3.react-router的 BrowserHistory  和HashHistory的区别，以及 HashHistory和路由无法匹配的问题
比如 浏览器地址栏的#/1203/Chart 就无法跟 path={'#/' + pathId + '/Chart} 发生匹配

原因是原来的router和route是从 react-router-dom引入的，改为如下即可：

        // import { BrowserRouter as Router, Route, Switch } from "react-router-dom";

        import { Router , Route} from 'react-router';


#### 4.webview在android下，空白部分占满全屏的问题

将webview的父组件view改为scrollview即可

#### 5.Native module VectorIconsModule tried to override VectorIconsModule for module name RNVectorIconsModule. If this was your intention, set canOverrideExistingModule=true
找到MainApplication.java(android/app/src/main/java/com)，里面的protected List<ReactPackage> getPackages()，有重复的引用，把重复的部分删除就行了

#### 6.android下，ant-design-rn的Popover组件内的TouchableOpacity，或者绝对定位组件内的TouchableOpacity点击事件无效
原因是 TouchableOpacity组件超出了父组件的空间范围，点击事件无效。将其放置在顶层组件内即可

#### 7.使用react-navigation时，组件不会完全卸载，判断当前路由的问题

此时直接查看this.props.navigation下的state是无法准确得到当前路由href的，目前的方案是在global.js里存一个常量，在screenTracking里跟随变化赋值
