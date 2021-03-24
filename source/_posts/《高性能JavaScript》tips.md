---
title: 《高性能JavaScript》tips
date: 2021-03-24 22:45
categories: 技术
tags: JS JS基础
toc: true
---

### 1.重绘（repaints）和重排（reflows）

元素宽、高、边框等影响“占地面积”的布局改变时，会产生重排，而改变颜色、背景等只会触发重绘而不会产生重排

如何减少重排和重绘：

1.不要在设置元素属性后立即对属性求值，求值会导致浏览器强制马上重排或重绘界面UI

2.尽量一次性修改元素样式，比如使用className

3.考虑将元素先脱离文档（隐藏、拷贝元素到fragment等）

4.缓存布局信息（比如对元素offsetTop递增时，使用变量递增而不是offsetTop+1这样的递增）



### 2.展开循环以减少迭代次数

````javascript
const items = [1,2,3,4,5,6,7,8,9,10,11,12];
var iterations = Math.ceil(items.length / 8), // Match.ceil 向上取整  比如length=12，则此时等于2 即执行两次循环
    startAt = items.length / 8, // 取余数，比如length=12，则此时等于4
    i = 0;
function processFunc(e){
    console.log("e=", e)
}
do {
    console.log('do, ', startAt, i); // 第一次 4 ，0 第二次 0 4
    switch (startAt) {
        case 0: processFunc(items[i++]); // 没有break语句，在命中某个case之后，后面的case会全部执行
        case 7: processFunc(items[i++]);
        case 6: processFunc(items[i++]);
        case 5: processFunc(items[i++]);
        case 4: processFunc(items[i++]);
        case 3: processFunc(items[i++]);
        case 2: processFunc(items[i++]);
        case 1: processFunc(items[i++]);
    }
    startAt = 0;
} while (--iterations);
````



### 3.任务分片

对于不需求立即获取结果的任务，可以使用任务分片，通过定时器分片处理此任务，避免长时间占用线程导致界面卡顿（因为js线程一直占用，无法更新UI，导致看上去界面没有反应）

````javascript
// 原函数
function saveDocument(id){
    openDocument(id);
    whiteText(id);
    closeDocument(id);
    updateUI(id);
}
// 改造
function saveDocument(id){
    var tasks = [openDocument, whiteText, closeDocument, updateUI];
    var curDo = tasks.shift(); // 取数组中的第一项，作为当前任务
    curDo(id);
    setTimeout(function(){
        if(task.length){
            saveDocument(id);
        }
    }, 25)
}
// 针对较大的数组，使用时间限制
function timedProcessArray(items, process, callback){
    var todo = items.shift();
    var startTime =  curTime = new Date();
    var curTime = startTime;
    while((new Date()) - startTime<50 && items.length){
        process(todo)
    }
    if(items.length){
        setTimeout(function(){
            timedProcessArray(items, process, callback)
        }, 50)
    }else{
        callback();
    }
}
````

