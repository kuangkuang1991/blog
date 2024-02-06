---
title: 《CSS新世界》读书笔记
date: 2023-11-20 22:35
categories: 技术
tags: 
- 技术
- 读书笔记
- css
toc: true
---

1.initial/unset/revert 关键字区别
initial: CSS标准定义的初始值，比如font-size的初始值为medium
unset: 不固定关键字，有继承性时表现为inherit，没有继承性时表现为initial
revert: 浏览器内置样式

2.all属性指代几乎所有属性，方便重置：
````css
input{
  all: inherit
}
````

3.width: fit-content 指定尺寸为元素尺寸，但不超出容器尺寸

4.逻辑属性：start/end，通常配合direction属性使用，亦或者在聊天窗口之类的对称布局中使用

5.最常用的逻辑属性：inset，比如设置inset:0用于缩写替代left:0;right:0;top:0;bottom:0;

6.border-image：更自由的border效果

7.position: sticky 粘性定位

8.善用word-break：keep-all配合空格（如“张三 李四 王二麻子”）保护中文词组不被断开

9.使用<wbr>换行声明标签来精准控制换行

10.颜色透明度的十六进制

00%=FF（不透明）   
5%=F2    
10%=E5    
20%=CC    
30%=B2     
40%=99    
50%=7F    
60%=66    
70%=4c    
80%=33    
90%=19    
100%=00（全透明）

11.background-size的值
 cover: 覆盖，可能溢出
contain: 包含，可能留空

12.background多背景实现加/减图标
````css
box{
  background: linear-gradient(currentColor, currentColor) no-repeat center/.875em 2px, 
                linear-gradient(currentColor, currentColor) no-repeat center/2px .875em,
                ghostwhite;
}
````
更多多背景效果实现：https://projects.verou.me/css3patterns/

13.background-clip 限制背景显示区域
可用值：border-box，padding-box，content-box，text
background-clip: text实现渐变文字背景：
````css
.text-gradient {
        background: linear-gradient(deepskyblue, deeppink);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
}
````

14.background-origin 规定了指定背景图片background-image属性的原点位置的背景相对区域

15.background-repeat: space(均匀平铺背景图), round(重复平铺背景图并拉伸，保证不留空隙)

16.background-position指定起始方位
````css
background: url(1.jpg) no-repeat right 20px bottom 20px;
````
background-position也支持逐一设置多背景中每个背景的位置

17.outline-offset设置负值避免被相邻元素遮挡

18.一些不指定层叠属性但能创建层叠上下文的方式：
比如opacity: 0.99 让顺序在前的元素覆盖后面的元素
transform不为none

19.opacity父子元素同时使用时效果叠加，子元素 = 父opacity*子opacity

20.border-radius可以指定水平半径和垂直半径：30px/60px(椭圆的圆角效果)，也可以设置更多不规则圆角效果

21.box-shadow内阴影inset可以模拟边框（替代透明边框方案），也可以给元素加一层背景色，实现点击按钮的颜色加深效果

22.box-shadow的第四个长度值扩展半径，设置负值时让阴影只在一侧显示

23.box-shadow支持多阴影，方便实现多种效果：
多边框效果
渐变边框效果
loading的多个圆点效果
云朵效果
3D投影效果

24.transform多种特性细节:
- 尺寸不会变化
- 内敛元素无效
- 产生锯齿和虚化：只能依靠更高分辨率的显示器
- 应用多种变换时，不同顺序产生的效果不同

25.CSS高性能动画三要素：
绝对定位
opacity属性
transform属性

26.元素应用transform属性后的变化
- 创建层叠上下文
- 固定定位失效（如fixed）
- 改变overflow对绝对定位元素的限制
- 改变绝对定位元素的包含块（transform不为none即可等同于position:relative）

27.了解三个函数
 - min()
- max()
- clamp(min, val, max) = max(MIN, min(VAL, MAX))

28.transition-delay使用负值来跳到对应动画帧

29.transition-property不支持display，但支持visibility

30.animation支持多个动画的组合，方便复用

31.@keyframes规则
- 起止关键帧可以不设置
- 关键帧列表可以合并（0%, 50%, 100%{xxx}）
- 不同的关键帧选择器时无序的
- 重复定义的关键帧不是完全被覆盖（定义的不同属性会合并，相同属性覆盖）
- 样式可以不连续
- !important无效（连带定义的属性值无效）
- 优先级最高

32.animation-direction的两个值
- reverse 定义动画方向是反过来（从to到from）
- alternate 可以实现钟摆来回摆动效果

33.动画次数可以是小数，实际效果就是按百分比执行

34.aimation-fill-mode 动画填充模式
- forwards 动画结束后，元素应用结束时的属性值
- backwards 动画结束后应用最初的属性值

35.animation动画可以暂停，继续播放和重启
- 暂停/继续播放：将animaiton-play-state的值由running改为paused
- 重启：ele.offsetWidth //触发重绘

36.animation-timing-function: step(number, position)实现跳跃动画

37.分栏布局columns: 2 将一个垂直的列表分成两列
column-width 理想宽度 / column-count 理想数目：最终实现不会完全按照该数值
column-gap是 gap的子属性
column-rule 分割线样式，语法同border
column-span：子元素设置，是否跨栏显示

38.flex弹性布局
父元素设置display: flex后子元素的变化：
 - 子项块状化（文本元素也有效）
 - 子项浮动无效
 - 子项支持z-index
 - 子项margin值不会合并
 - 子项巧用margin: auto可以撑开剩余空间
 - 子项设置绝对定位，会脱离弹性布局
 - 子项想要设置尺寸，可以使用flex-basis
flex-flow是flex-direction+ flex-wrap的缩写

39.flex对齐属性
justify-content: space-evenly 空白间距完全相等
align-items是设置在容器上的，align-self是设置在某个子项上的
align-items是控制每一个子项的垂直对齐方式，align-content是将子项作为整体设置
order调整子项的位置

40.flex属性的缩写
````css
/* 1个值是数值，表示flex-grow */
flex：1;
/* 1个值是长度值，表示flex-basis */
flex：100px;
/* 2个值，第1个值一定是flex-grow，数值就是flex-shrink，长度值就是flex-shrink */
flex：1 100px;
/* 2个值，flex-grow和flex-shrink  */
flex：1 1;
/* 3个值 */
flex：1 1 100px;
````

41.flex的分配
flex-grow 决定了剩余空间的分配比例，默认为0，
flex-shrink 决定了剩余空间不足的分配比例，默认为1（数值越大，最终长度越小）
flex-basis决定了分配的基础尺寸
经典案例见：https://demo.cssworld.cn/new/6/2-9.php

42.flex:0/none/auto/initial的场景
 - flex:initial等同于设置flex:0 1 auto;表现为空间剩余时不扩张（0），空间不足时会收缩（1），同时尺寸自适应内容（auto）
 - flex:0等同于设置flex: 0 1 0%;（最小内容宽度，文字会一柱擎天）
 - flex: none等同于设置flex: 0 0 auto;（最大内容宽度，溢出不换行，适合文字较少，不会换行的元素，比如按钮）
 - flex: 1 等同于设置flex: 1 1 0%; （充分利用剩余空间，同时不侵占其他元素空间，适合等分父元素）
 - flex: auto 等同于设置flex: 1 1 auto; （充分利用剩余空间，同时尺寸按照内容分配，适合基于内容动态分配，比如导航菜单不同内容长度时的按内容长度分配）

43.flex尺寸计算优先级：
最大最小尺寸限制 > 弹性增长或收缩 > 基础尺寸或内容尺寸（基础尺寸由flex-basis活 width指定，内容尺寸指最大内容宽度）

44.flex-basis属性与尺寸计算
 - 外在尺寸够大，内容尺寸够小，则width和flex-basis 的表现基本一致（flex-basis可能会被没有换行点的最小内容宽度拉长，大于width）
 - width和flex-basis同时设置数值，则width被忽略
 - flex-basis设置auto，则使用width作为基础尺寸
 - 如果flex-basis 和 width都是auto，则使用子项的最大内容宽度作为基础尺寸
 
45.grid布局

46.css shape布局

47.@media规则由4部分组成
 - 媒体查询修饰符only/not （only已无意义）
 - 媒体类型 screen
 - 媒体条件 and
 - 媒体特性max-width

48.使用媒体查询检测触摸设备
````CSS
@media (hover: none){  /*表示设备无法悬停*/
  figcaption{
    display: block;
  }
}
````

49.vh的妙用：内容不足一屏时，底栏紧贴窗口底部；内容超过一屏时，底栏紧贴页面最下方
````HTML
<div class="container">
  <content></content>
  <footer></footer>
</div>
````
````CSS
.container{
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}
footer{
  margin-top: auto;
}
````

50.touch-action控制设备的触摸行为
取消300ms点击延迟：对html标签设置touch-action: manipulation; // 不允许双击缩放
还可以限制滚动方向等

51.css自定义属性可以声明类型包括：颜色值，数值，起止色值，方向，渐变参数或渐变表达式；

52.js设置css自定义属性：box.style.setProperty('--color', 'red');
js获取自定义属性值：getComputedStyle(box).getPropertyValue('--color');

53.使用object-fit控制图片尺寸：
fill：填充，内容会被拉伸，不保证原始比例
contain：包含，保持比例，但可能留白（适合列表）
cover：覆盖，保持比例，但可能溢出（适合缩略图）
none：保持比例，无视外部尺寸
scale-down：较大时等于contain，较小时等于none（适合全屏大图预览）

54.使用object-position 控制替换内容的位置，默认值：50% 50%，常用在头像展示将脸部居中，也可以配合雪碧图来实现动画

55.cross-fade()函数让两张图像半透明混合

56.element()函数让任意DOM元素的渲染效果变成图像（跟随原始元素同步变化）（仅firefox支持）

57.放大图片以解决模糊图片的边缘透明效果：
````CSS
.box img{
  transform: scale(1.1);
  filter: blur(5px);
}
````

58.brightness()函数实现黑白着色效果
````CSS
.button-button .icon{
  filter: brightness(100);  /* 让图标变成白色 */
}
````

59.drop-shadow()可以实现镂空投影效果

60.backdrop-filter 可以实现背景毛玻璃效果

61.mask+mask-image实现半透明的PNG图像遮罩

62.clip-path 用来对任意元素的可视区域进行裁剪（不影响元素布局）

63.offset属性实现不规则路径运动

64.滚动相关
scroll-behavior:smooth 实现平滑滚动
overscroll-behavior:contain 实现子元素滚动结束不出发父元素滚动
overflow-anchor滚动锚定（新插入内容不影响当前滚动查看的位置）
css scroll snap 定义滚动结束自动调整位置

65.pointer-events 子元素的属性值可以覆盖祖先元素，适合在外层元素禁用点击时，内层子元素依然响应鼠标操作

66.resize属性允许元素拉伸：本质是改变width和height

67.cater-color 改变光标颜色

68.user-select: all 允许选中整个元素（适合卡片选择 或 复制整段文本）

69.::selection伪元素改变选中文字的颜色和背景色