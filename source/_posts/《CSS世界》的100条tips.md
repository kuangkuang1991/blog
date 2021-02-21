---
title: 《CSS世界》的100条tips
date: 2021-02-21 21:10
categories: 随笔
tags: 杂感
toc: true
---

标题是唬人的，实际只记录了39条。如下(使用了html效果的，请直接右键查看html源码)：

1.display为block的元素不必设置width，否则丢失流动性（流动性即可以水平占满空间）

<div style="border:1px solid grey;margin-bottom:10px">
  <div style="background-color:red;width:50px;height:50px"></div>
</div>

2.格式化宽度：绝对定位元素宽度由外部元素决定（通常情况由内部元素决定，即包裹性）

<div style="border:1px solid grey;margin-bottom:10px;width:200px;height:100px;position:relative;">
  <div style="background-color:red;height:50px;position:absolute;left:20px;right:20px;"></div>
</div>

````css
div{
    position:absolute;
    left:20px;
    right:20px;
}
````

3.包裹性：按钮文字越多宽度越宽，但是会在容器宽度处换行

<div style="border:1px solid grey;margin-bottom:10px;width:50px;height:100px;">
  <button style="">我是按钮2221</button>
</div>

参考：https://demo.cssworld.cn/3/2-4.php

借此特性实现：文字少时居中，文字多时靠左显示

````css
.box{
    text-align:center;
}
.content{
    display:inline-block;
    text-align:left;
}
````

<div style="border:1px solid grey;margin-bottom:10px;width:200px;height:100px;text-align:center;">
  <div style="display:inline-block;background-color:red;width:50px;text-align:left">我是居中的div</div>
</div>

4.height:100% 无效。规范说明：如果包含块的高度没有显式指定，并且该元素不是绝对定位，则计算值是auto

5.max-width会覆盖width!important，min-width会覆盖max-width

https://demo.cssworld.cn/3/3-1.php

6.可以使用max-height：一个足够大的值，来实现展开收起动画的内容的动态高度

7.幽灵空白节点（font-size:0则高度为0）

````css
div{
    background-color:#98b1d4;
    line-height:18px;
}
span{
    display:inline-block;
}
<div><span></span></div> // 此时会有18px的高度，即空白节点的高度
````

<div style="background-color:#98b1d4;margin-bottom:10px;line-height:18px;">
  <span style="display:inline-block;"></span>
</div>

8.替换元素特性：

>1.内容外观不受页面css的影响
>
>2.有自己的尺寸（比如video、iframe、canvas，默认尺寸300*150，或者img为0，表单元素与浏览器有关）
>
>3.css属性有自己的规则，比如vertical-align，为元素下边沿
>
>4.所有替换元素都是内联水平元素（inline-block、inline、none）
>
>5.尺寸计算分三类，依照优先级：css尺寸、html尺寸和固有尺寸
>
>6.替换元素和非替换元素只差一个src（或者content）属性。详见：https://demo.cssworld.cn/4/1-4.php

9.padding的特殊情形：

>1.过大的padding值会让box-sizing:border-box无效

````css
div{
    width:80px;
    padding:20px 60px;
    box-sizing:border-box;
} 
最终宽度为120px(60px*2)(表现为首选最小宽度)
````

<div style="background-color:#98b1d4;margin-bottom:10px;width:80px;padding:20px 60px;box-sizing:border-box;">
</div>

> 2.内联元素的宽度和高度都受到padding的影响（可以用于增加点击区域，或者锚点定位降低顶部高度），见案例：https://demo.cssworld.cn/4/2-1.php

````css
a{
    padding:50px;
    background-color:#98b1d4;
}
````
<div style="border:1px solid grey;margin-bottom:60px">
<a style="padding: 50px;background-color:#98b1d4">我是测试的链接</a>
</div>

10.padding的百分比值是相对于(父元素)宽度width计算的。因为可以据此实现正方形：(或者参考：https://demo.cssworld.cn/4/2-3.php)

````css
div{
    padding:50%;
}
````
<div style="border:1px solid grey;margin-bottom:10px;width: 100px;">
    <div style="background-color:#98b1d4;padding:50%;"></div>
</div>

11.margin不能改为已设定width的元素宽度，但是可以改变未设定width的块元素宽度

````css
div{
    width:50px;
    margin:0 -20px;//无效果，宽度依然是50px
}
````
<div style="border:1px solid grey;margin-bottom:10px;">
    <div style="background-color:#98b1d4;width:50px;margin:0 -20px;height: 50px;"></div>
</div>

````css
<div class='father'><div class='son'></div></div>
.father{
    width:50px;
}
.son{
    margin:0 -20px;//宽度是88px(50+40-1*2)
}
````
<div style="border:1px solid grey;margin-bottom:10px;width:50px;">
    <div style="background-color:#98b1d4;margin:0 -20px;height: 50px;"></div>
</div>

> 只要元素尺寸表现符合“充分利用可用空间”，无论是垂直方向还是水平方向，都可以通过margin改变尺寸。	

12.margin合并

三种情形：

> 1.相邻兄弟元素margin合并

````html
<style>
    p{
        margin: 1em 0;
    }
</style>
<body>
    <p>第一行</p>
    <p>第二行</p>
</body>
````

<div style="border:1px solid grey;margin-bottom:10px;">
    <p style="background-color:#98b1d4;margin:1em 0">p元素1</p>
    <p style="background-color:#98b1d4;margin:1em 0">p元素2</p>
</div>

> 2.父元素和第一个/最后一个子元素。参考：https://demo.cssworld.cn/4/3-3.php

<div style="background:red;margin-bottom:10px;">
    <p style="background-color:#98b1d4;margin-top:20px">p元素1</p>
    <p style="background-color:#98b1d4;">p元素2</p>
</div>

即子元素设置margin-top，导致父元素产生了margin-top的效果。可以通过以下方法去除

> a.父元素设置BFC
>
> b.父元素设置border-top
>
> c.父元素设置padding-top
>
> d.父元素和第一个子元素之间添加内联元素进行分隔

> 3.空块级元素的margin合并。即空块元素的margin-top和margin-bottom合并

13.margin填充规则：见http://demo.cssworld.cn/4/3-4.php

> 1.如果一侧是定值，一侧auto，则auto为剩余空间大小
>
> 2.如果两侧均是auto，则平分剩余空间

可以使用margin-left：auto实现块状元素靠右对齐。或者margin：0 auto实现居中对齐。或者实现垂直居中：
````css
.son{
    margin-left:auto;
    width:300px;
}
````
或者
````css
.son{
    position:absolute;
    top:0;right:0;bottom:0;left:0;
    width:200px;height:200px;
    margin:auto;
}
````
<div style="background:red;margin-bottom:10px;">
    <div style="background-color:#98b1d4;margin-left:auto;width:300px;">内部div</div>
</div>

14.border-color默认值就是color色值（类似还有outline、box-shadow、text-shadow）

<div style="border:1px solid #98b1d4;margin-bottom:10px;">
    <div style="color:red;border:1px solid;margin: 5px;">内部div</div>
</div>

15.vertical-align:middle 指的是基线（字母x下边沿）往上1/2 x-height的高度。也就是字母x的中间点

16.对于非替换元素的内联元素，可视高度完全由line-height决定

<div style="border:1px solid #98b1d4;margin-bottom:10px;">
    <p style="line-height:36px;background-color:red;">p元素</p>
</div>

17.替换元素的高度不受line-height影响

<input value='123' style="line-height:36px;" />

18.关于文本的垂直居中：

> 1.单行文本，只需要line-heihgt即可垂直居中
>
> 2.多行文本垂直居中：见（ https://demo.cssworld.cn/5/2-4.php ）

````css
.box{
    line-heihgt:120px;
    background-color:#f0f3f9;
}
.content{
    display:inline-block;
    line-height:20px;
    margin:0 20px;
    vertical-align:middle;
}
<div class="box">
	<div class="content"></div>
</div>

````

<div style="border:1px solid #98b1d4;margin-bottom:10px;line-height:120px;">
    <div style="display:inline-block;line-height:20px;margin:0 20px;vertical-align:middle;">基于行高实现的多行文字垂直居中效果，需要vertical-align属性帮助。基于行高实现的多行文字垂直居中效果，需要vertical-align属性帮助。基于行高实现的多行文字垂直居中效果，需要vertical-align属性帮助。基于行高实现的多行文字垂直居中效果，需要vertical-align属性帮助。</div>
</div>

19.内联元素line-height的大值特性：无论内联元素如何设置，最终父级元素的高度都是由数值大的那个line-height决定

<div style="border:1px solid #98b1d4;margin-bottom:10px;">
    <span style="line-height:10px;">文本1</span><span style="line-height:30px;">文本2</span><span style="line-height:60px;">文本3</span>
</div>

20.vertical-align属性值分四类：

> 1.线类，如baseline（默认值，等于数值0）、top、middle、bottom
>
> 2.文本类，如text-top、text-bottom
>
> 3.上标下标类，如sub、super
>
> 4.数值百分比类，如20px、2em、20%（相对于line-height计算）等，正值向上偏移，负值向下。参见：https://demo.cssworld.cn/5/3-2.php

vertical-align只能应用于内联元素或者display:table-cell的元素。（如果内联元素设置了浮动或绝对定位，则失效）

21.float特性：参见：https://demo.cssworld.cn/6/1-1.php

> 1.包裹性
>
> 2.块状化并格式化上下文
>
> 3.破坏文档流（父元素高度塌陷）
>
> 4.没有margin合并

22.clear：元素盒子的边不能和前面的浮动元素相邻。见例子：

````css
li{
    width:20px;height:20px;
    margin:5px;
    float:left;
}
li:nth-of-type(3){
    clear:both;
}//实际结果：两行显示

````

<ul style="border:1px solid #98b1d4;margin-bottom:10px;">
    <li style="float:left;background-color:red;width:20px;height:20px;margin:5px;">div1</li>
    <li style="clear:both;background-color:yellow;">div2</li>
</ul>

23.clear属性只能块级元素有效，因此使用伪元素清除浮动需要设置display：block（或table、list-item等）。另附特殊情况：https://demo.cssworld.cn/6/2-1.php

24.BFC（block formatting context）块级格式化上下文：内部元素不管怎么折腾，都不会影响外部的元素。触发条件：

>hmtl根元素
>
>float的值为left、right
>
>overflow的值为auto、scroll或hidden
>
>display的值为table-cell、table-caption和inline-block
>
>position的值为absolute、fixed

25.锚点定位小技巧：

>  返回顶部

````html
<a href="#">返回顶部</a>

````

<a href="#">返回顶部</a>

> 快速将输入框移动到屏幕内

````javascript
document.querySelector('input').focus();

````

> 设置了overflow：hidden或者scroll的元素也可以使用锚点定位，将当前不可见的元素移动到可见范围

<div style="border:1px solid #98b1d4;margin-bottom:10px;width:100px;height:50px;overflow:hidden">
    <div style="background-color:red;height:70px;width:100px;">
    内部div内部div内部div内部div内部div内部div内部div内部div内部div
    <input  id="testInput" />
    </div>
</div>

<a id="testTarget" style="cursor:pointer">点击将隐藏的input可见</a>

<script>

var aDom = document.querySelector('#testTarget');
aDom.addEventListener("click", function(){
    console.log("123");
    document.querySelector('#testInput').focus();
})

</script>

> 无js实现tab切换。参见：https://demo.cssworld.cn/6/4-2.php 和 https://demo.cssworld.cn/6/4-3.php 

26.设置绝对定位的span元素，display计算值变成block

---待重读：absolute的包含块

27.无依赖的绝对定位：

​	单独使用position：absolute即可实现定位（还在元素的原位置），参见https://demo.cssworld.cn/6/5-4.php 

​	或者实现图标和文字的对齐：https://demo.cssworld.cn/6/5-5.php

​	或者实现表单的提示信息：https://demo.cssworld.cn/6/5-6.php  

​	position：absolute的元素设置text-align:center会影响原内联元素，本质上是影响“幽灵空白节点”，参见：https://demo.cssworld.cn/6/5-8.php

<div style="border:1px solid #98b1d4;margin-bottom:20px;position:relative;height:50px;">
    <span style="position:absolute;">文本</span>
</div>

28.如果overflow不是定位元素，同时绝对定位元素和overflow容器之间也没有定位元素，这overflow无法对absolute元素进行裁剪。

>overflow元素只设置transform的时候，chrome下的overflow裁剪无效

>裁剪属性clip必须和固定定位(position:relative)和绝对定位(position:absolute)元素一起使用

>绝对定位元素在对立方向同时发生定位时，具有流体特性（类似diplay:block）。你用流体特性和margin:auto实现居中：

   ````css
   div{
       width:300px;height:300px;
       position:absolute;
       left:0;right:0;top:0;bottom:0;
       margin:auto;
   }
   
   ````

<div style="border:1px solid #98b1d4;margin-bottom:30px;position:relative;">
    <span  style="position:absolute;left:0;right:0;background-color:red;">文本</span>
</div>

29.relative相对定位：不影响其他元素

> 1.与margin的区别：https://demo.cssworld.cn/6/6-1.php 
>
> 2.相反方向同时定位：top比bottom优先，left比right优先

<div style="border:1px solid #98b1d4;margin-bottom:20px;">
    <span  style="position:relative;left:20px;top:0">文本</span>
    <span>文本2</span>
</div>

30.position：fixed不设置left、top等（只配合margin），可实现无依赖的固定定位

<div style="border:1px solid #98b1d4;margin-bottom:20px;position:relative;">
    <span style="position:fixed;margin-left: 20px;margin-top: 20px;">文本</span>
</div>

31.z-index层叠顺序

> 【层叠上下文background和border】<【负的z-index】 < 【block块状水平盒子】 < 【float浮动盒子】 < 【inline水平盒子】 < 【z-index:auto或者看成z-index:0 】 < 【正的z-index】
>
> 基本规则：1.谁大谁上；2.后来居上
>
> z-index:0 和z-index:auto的区别见https://demo.cssworld.cn/7/5-1.php。当父元素z-index:auto时，子元素就不受父元素影响，只需要比较子元素；当父元素z-index:0时，就会创建一个层叠上下文，忽略子元素的z-index。按后来居上顺序
>
> 定位元素层叠在普通元素之上。原因是其z-index:auto（默认值）生效，也就是0级别

理解层叠上下文：https://demo.cssworld.cn/7/6-1.php 

32.font-size的关键字属性值

>相对尺寸关键字：
>larger,smaller，相对于当前元素的font-size计算

>绝对尺寸关键字，受浏览器设置的字号影响
> xx-large, x-large, large, medium等

或者数值0.实现隐藏文字的作用

<div style="border:1px solid #98b1d4;margin-bottom:20px;">
    <span  style="font-size:0">文本</span>
</div>

33.font-family的值有两类：

> 字体名，如simsun、'Microsoft Yahei'（有空格的需要引号引起来）
>
> 字体族，如serif（衬线字体，笔画末尾有修饰，如宋体），sans-serif（无衬线字体，如微软雅黑）

font-family值可以有多个，用逗号分隔，浏览器会依次往后查找对应字体。

<div style="border:1px solid #98b1d4;margin-bottom:20px;position:relative;">
    <span  style="font-family:'Source Han Sans CN','PingFang-SC-Medium','Microsoft YaHei';">文本</span>
</div>

34.text-indent的特性

> text-indent仅对第一行内联盒子内容有效；
>
> 非替换元素以外的display计算值为inline的内联元素设置text-indent无效；
>
> input标签设置text-indent无效
>
> button标签设置text-indent有浏览器差异

<div style="border:1px solid #98b1d4;margin-bottom:20px;width:50px;text-indent: 10px;">
    <span  style="text-indent:10px;background-color:red;">文本文本文本文本文本</span>
</div>

35.white-space本质上是决定回车、空格是否生效

> normal 合并空白字符和换行符
>
> pre 空白字符不合并，并且内容只在有换行符的地方换行
>
> nowrap 与normal一样合并空白字符，但不允许文本环绕
>
> pre-wrap 空白字符不合并，并且只在有换行符的地方换行，同时允许文本环绕
>
> pre-line 合并空白字符，但只在有换行符的地方换行，允许文本环绕

<div style="border:1px solid #98b1d4;margin-bottom:20px;">
    <span  style="white-space:normal;display:inline-block;">文本   1</span><br />
    <span  style="white-space:pre;">文本   1</span><br />
    <span  style="white-space:nowrap;">文本   1</span><br />
    <span  style="white-space:pre-wrap;">文本   1</span><br />
    <span  style="white-space:pre-line;">文本   1</span><br />
</div>

36.text-align:justify要实现两端对齐，需要满足：

> 有分隔点，如空格
>
> 超过一行，此时非最后一行内容会两端对齐

<div style="border:1px solid #98b1d4;margin-bottom:20px;text-align:justify;width:50px;display:inline-block;">
    文本文本文  本文本文本
</div>

37.color的特性

> ~~不支持transparent值和currentColor变量~~   已支持transparent值和currentColor变量
>
> ~~不支持rgba颜色（rgba(255,0,0,0.5)）和hsla颜色(hsla(240,100%,50%, .7))~~ 已支持rgba颜色和hsla颜色
>
> 支持系统颜色，如WindowFrame等

<div style="border:1px solid #98b1d4;margin-bottom:20px;color:yellow;">
    <span style="color:transparent;">文本1</span>
    <span style="color:currentColor;">文本2</span>
    <span style="color:rgba(255,0,0,0.5);">文本3</span>
    <span style="color:hsla(240,100%,50%, .7);">文本4</span>
    <span style="color:WindowFrame;">文本5</span>
</div>

38.background-position特性：

> 缺省值默认为center，如background-position: top center 等同于 background-position: top
>
> 百分比值计算：positionX = (容器宽度 - 图片宽度)*percentX 

39.HTML5新增布尔属性：hidden

````html
<div hidden>
    不显示的内容
</div>
````
<div style="border:1px solid #98b1d4;margin-bottom:20px;height:20px;">
    <div hidden>
        不显示的内容
    </div>
</div>

