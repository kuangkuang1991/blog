---
title: 《JavaScript高级程序设计》的100条tips（下）
date: 2021-02-27 22:15
categories: 技术
tags: JS JS基础
toc: true
---

#### 1.全局变量和window属性的区别

全局变量不能通过delete操作符删除，而window上定义的对象可以

````javascript
var age = 29;
window.color = "red";

delete window.age;

delete window.color;

alert(window.age); // 29 (全局变量没有被delete删除)
alert(window.color); // undefined （window属性被delete删除）
````

#### 2.setTimeout和setInterval

第二个参数告诉JavaScript，再过多长时间把当前任务添加到列队中。如果列队是空的，那么添加的代码会立即执行；如果列队不为空，那么就要等前面的代码执行完后才能执行。

可以使用setTimeout代替setInterval，因为setInterval的某个func可能会在前一个func没结束就执行

#### 3.节点的读取和操作

a.每个节点都有nodeType、nodeName、nodeValue属性

b.访问NodeList中的节点：

````javascript
var firstChild = someNode.childNodes[0];
var secondChild = someNode.childNodes.item[1];
var count = someNode.childNodes.length
````

c.操作节点

appendChild() 像childNodes列表的末尾插入一个节点，并返回插入的节点。（插入后各节点立刻得到更新）

````javascript
var returnedNode = someNode.appendChild(newNode);// 接收一个参数，即要插入的节点
alert(returnedNode == newNode); // true
alert(someNode.lastChild == newNode); // true
// 如果传入appendChild()的节点已存在，则移动该节点
````

insertBefore() 把节点插入到特定位置。

````javascript
// 只传一个参数，变成最后一个子节点
returnedNode = someNode.insertBefore(newNode, null); 
alert(returnedNode == someNode.lastChild); // true

// 传两个参数，变成第一个子节点
returnedNode = someNode.insertBefore(newNode, someNode.firstChild);
alert(returnedNode == someNode.firstChild); // true
````

replaceChild() 替换节点（传参：要替换的节点，被替换的节点）

````javascript
returnedNode = someNode.replaceChild(newNode, someNode.firstChild); 
````

removeChild() 移除节点

#### 4.H5与类相关的扩充

a.获取节点：getElementsByCalssName()

b.操作类名：

````javascript
div.classList.remove("user"); //移除类名
div.classList.add("current"); //添加类名
div.classList.toggle("current"); //切换类名（如果有就删除，没有就添加）
div.classList.contains(); //是否包含
````

#### 5.焦点管理

document.activeElement: 引用当前获得了焦点的元素

document.hasFocus() 判断文档是否获得了焦点

#### 6.自定义数据属性

````html
<div id="myDiv" data-appId="12345"></div>
````

使用元素的dataset属性获取自定义属性的值：

````javascript
var appId = div.dataset.appId; // "12345"
````

#### 7.专有扩展

特指部分浏览器先实现，后来被纳入标准的属性或方法

a.children属性(与childNodes的区别：children是非标准属性，只返回HTML子节点)

b.contains()方法：判断被检测的节点是否为后代节点。类似还有comoareDocumentPosition()方法，判断两个节点的关系（前、后、包含和被包含等）

c.innerText和outerText：非标准属性

d.滚动：（部分浏览器实现）

> scrollIntoViewIfNeeded(alignCenter)：当前元素不可见时滚动至可见
>
> scrollByLines(lineCount)：滚动指定的行高
>
> scrollByPages(pageCount)：滚动指定的页面高度

#### 8.样式

> cssText 访问或设置style特性的css代码
>
> getPropertyValue(propertyName)：返回给定属性的字符串值
>
> removeProperty(propertyName)：从样式中删除给定属性
>
> setProperty(propertyName, value, priority)：将给定属性设置为相应值，并加上优先权标志
>
> getComputedStyle() 返回计算的样式（返回一个对象，从对象中取值）
>
> 样式表操作：addRule()，removeRule()等

#### 9.元素大小

a.偏移量

元素可见大小由其高度、宽度决定，包括内边距、滚动条和边框大小。不包括外边距。

> offsetHeight: 元素在垂直方向上占用的空间大小。包括高度、可见水平滚动条高度、上边框高度和下边框高度
>
> offsetWidth: 元素在水平方向占用的空间大小
>
> offsetLeft: 元素的左外边框至包含元素的左内边框之间的像素距离
>
> offsetTop: 元素的上外边框至包含元素的上内边框之间的像素距离

![img](https://pic3.zhimg.com/80/v2-7f77ebdd1040d03cb069700d3084d79e_720w.jpg)

b.客户区大小

元素内容及其内边距所占据的空间大小（不包括边框、滚动条）

> clientWidth: 元素内容区宽度加上左右内边距宽度
>
> clientHeight: 元素内容区高度加上上下内边距高度

![clientWidth和clientheight 主要表示](https://img-blog.csdnimg.cn/20190830174821356.PNG)

c.滚动大小

包含滚动内容的元素的大小

> scrollHeight: 没有滚动条的情况下，元素内容的总高度
>
> scrollWidth: 没有滚动条的情况下，元素内容的总宽度
>
> scrollLeft: 被隐藏在内容区域左侧的像素数。设置此属性可以改变元素的滚动位置
>
> scrollTop: 被隐藏在内容区域上方的像素数。设置此属性可以改变元素的滚动位置

![scroll-dimension](http://git-hexo-blog.oss-cn-beijing.aliyuncs.com/scroll-dimension.png)

#### 10.遍历（都是深度优先）

NodeIterator接收四个参数：

> root: 想要作为搜索起点的树中的节点
>
> whatToShow: 表示要访问哪些节点的数字代码
>
> filter: NodeFilter对象，或者应该接收还是拒绝某些特定节点的函数
>
> entityReferenceExpansion: 布尔值，表示是否要扩展实体引用

````javascript
var div = document.getElementById("div1");
var iterator = document.createNodeIterator(div, NodeFilter.SHOW_ELEMENT, NULL, false);
var node = iterator.nextNode();
while(node != null){
    alert(node.tagName); //输出标签名
    node = iterator.nextNode(); //另外还有previousNode()方法
}
````

TreeWalker：用法类似

#### 11.DOM的范围

使用document.createRange()来创建DOM范围

````javascript
var range1 = document.createRange(),
    range2 = document.createRange(),
    p1 = document.getElementById("p1");
range1.selectNode(p1);
range2.selectNodeContent(p1);
````

![img](https://images2015.cnblogs.com/blog/943101/201701/943101-20170105195620159-1016665497.png)

#### 12.事件流和事件处理

“DOM2级事件”规定事件流包括三个阶段：事件捕获、处于目标和事件冒泡

DOM0 级事件处理程序：

````javascript
var myLink = document.getElementById("myLink");
myLink.onclick = function(event){
    event.preventDefault(); // 阻止默认事件, 只有cancelable为true的事件才能使用
} //onclick 也是btn标签的属性之一
````

DOM2 级事件处理程序：

````javascript
var btn = document.getElementById("myBtn");
btn.addEventListener("click", function(event){  //IE 中是 attachEvent()
    alert(event.currentTarget == this); // true 在btn上绑定的事件 ，这三个值相同
    alert(event.target == this); // true
    event.stopPropagation();// 停止冒泡
}); 
````

#### 13.鼠标事件相关

> 只有在一个元素上相继触发mousedown和mouseup事件，才会触发click事件。触发顺序：mousedown/mouseup/click

鼠标事件的位置

> a.客户区位置：clientX和clientY
>
> b.页面坐标位置：pageX和pageY(页面没有滚动时，与clientX和clientY相等，有滚动则一般pageX和pageY更大)
>
> b.屏幕坐标位置：screenX和screenY

修改键

> shiftKey / ctrlKey / altKey / metaKey 均为布尔值，属于event的属性，用来判断是否同时按下了键

相关元素

> mouseover和mouseout事件独有的relatedTarget属性，提供了鼠标移入移出的相关元素信息

鼠标按钮

> mousedown和mouseup事件独有button属性，用来判断是鼠标的哪个按钮被按下（0左、1中、2右）

mousewheel鼠标滚轮事件

#### 14.键盘事件相关

三个键盘事件：keydown、keypress、keyup（按执行顺序）。以及一个文本事件：textInput（DOM3）

event.keyCode // 键码

event.key // 键文本 (DOM3)

event.getModifierState() //  检测修改键是否被按下，传入修改键的字符串（如Shift、Control等）

textInput: 在可编辑区域触发，并且只会在按下输入实际字符的键才会触发。其event.data属性等于输入键的字符串。另外还有inputMethod属性（仅IE支持），表示输入的方式，比如1键盘输入，2粘贴输入，3拖放输入等

#### 15.DOM变动事件

````javascript
document.addEventListener("DOMSubtreeModified", function(e) {
	console.log("dom发生变动")
})
document.addEventListener("DOMNodeInserted", function(e) {
    console.log("有元素被加入")
})
document.addEventListener("DOMNodeRemoved", function(e) {
    console.log("有元素被移除")
})
document.addEventListener("DOMCharacterDataModified", function(e) {
    console.log("有dom文本发生变化")
})
document.querySelector(".textmodified").addEventListener("DOMCharacterDataModified", function(e) {
    console.log("设置在即将改变的div上的监听事件有dom文本发生变化")
})
````

#### 16.HTML5事件

a.contextmenu事件

b.beforeunload事件

c.DOMContentLoaded事件 和 readystatechange事件

d.hashchange事件

e.触摸设备事件：横竖屏、设备方向改变、设备移动等

f.触摸事件

> touchstart / touchmove / touchend / touchcancel
>
> 手势事件、、

#### 17.模拟事件

模拟鼠标事件：

````javascript
var btn = document.getElementById("myBtn");
// 创建事件对象
var event = document.createEvent("MouseEvents");
//初始化事件对象
event.initMouseEvent("click", true, true, document.defaultView, 0,0,0,0,0,false, false, false, false, 0, null);
//触发事件
btn.dispatchEvent(event);
````

#### 18.取得选择的文本

````javascript
var textbox = document.forms[0].elements["textbox1"];
textbox.selectionStart; //文本开始
textbox.selectionEnd; //文本结束
textbox.value = "Hello world!";
textbox.setSelectionRange(0, 3); //"Hel"
//要看到选择效果，需要调用select() 使文本框获得焦点
````

#### 19.富文本相关

可以使用document.execCommand()对文档执行预定义的命令，包括设置背景色、改变字体、插入元素、操作剪贴板等

````javascript
// 针对iframe富文本
frames["richedit"].document.execCommand("bold", false, null); //转换粗体
// 针对contenteditable属性为true的区块
document.execCommand("createlink", false, "http://www/wrox.com"); // 创建链接

// 富文本选区
var selection = frames["richedit"].getSelection(); 
var selectedText = selection.toString(); //取得选择的文本
````

#### 20.跨文档消息传递

````javascript
var iframeWindow = document.getElementById("myIframe").contentWindow;
// 两个参数：一个字符串数据，和一个消息接收方来自哪个域的字符串
iframeWindow.postMessage("A secret", "http://www.wrox.com");
//onmessage三个参数：data传入的字符串，origin发送方所在的域，source发送方的windows对象的代理(不是实际的window对象)
````

#### 21.原生拖放

拖动某元素时，依次触发下列事件

> dragstart，drag，dragend

当元素被拖放到一个有效的放置目标时，下列事件依次发生：

> dragenter，dragover，dragleave或drop

拖放的数据传递

> 使用dataTransfer对象的两个方法：getData() 和setData()
>
> 以及确定被拖动元素和目标元素能接收什么操作：dropEffect属性和effectAllowed属性

可拖动

> 默认情况下，图像、链接和文本可以拖动，另外还有H5属性draggable

#### 22.常见错误类型

> RangeError  数字超出允许的值范围时
>
> ReferenceError 当对变量/项的引用被破坏或不存在时，或者变量/项不存在。
>
> SyntaxError 语法错误 js无法理解时
>
> TypeError 类型错误 比如调用了数值类型不存在的字符串方法时
>
> URIError URI 传参错误
>
> EvalError 非法调用eval()
>
> InternalError 栈溢出，或者是无限递归

#### 23.JSON

JSON包含三种类型：简单值、对象和数组

其对象与JavaScript的对象字面量的区别：

> 1.JSON必须给属性加双引号（值不用加引号）
>
> 2.没有声明变量（没有变量 的概念），没有末尾的分号

````
{
	"name": "Nicholas",
	"age": 29 
}
````

也可以是数组

````json
[25, "hi", true]
````

JSON对象的两个方法：

> stringify() 把JavaScript对象序列化为JSON字符串
>
>  	a.使用stringify()序列化时，函数、原型和值为undefined的属性会被跳过
>
> ​	 b.另外还接收两个参数，第一个参数用于过滤（数组或函数），第二个参数用于控制缩进
>
> parse() 把JSON字符串解析为原生JavaScript值
>
>  parse()也可以接收另一个函数参数，功能与stringify()的类似

给对象定义toJSON()方法，则stringify()序列化时直接执行此方法

#### 24.跨域技术

a.图像Ping：简单、单向的跨域通信

````javascript
var img = new Image();
img.onload = img.onerror = function(){
    alert("Done!");
}
img.src = "http://www.example.com/test?name=Nicholas";
// 缺点：只能发送GET请求，无法访问服务器的响应文本
````

b.JSONP

````javascript
function handleResponse(response){
    ///接收服务器返回的数据
}
var script = document.createElement("script");
script.src = "http://freegeoip.net/json/?callback=handleResponse";
document.body.insertBefore(script, document.body.firstChild);
// 缺点：可能夹带恶意代码，无法确定是否失败
````

c.Comet

长轮询（服务器接收请求后一直保持连接直到发送数据）和流（只有一个HTTP连接，保持打开，间歇发送数据但不关闭）

d.SSE

#### 25.Web Sockets

````javascript
var socket = new WebSocket("ws://www.example.com/server.php");
var message = {
    time: new Date(),
    text: "Hello world!",
    clientId: "asdfp8734rew"
}
socket.send(JSON.stringify(message));
socket.onmessage = function(event){
    var data = event.data;
} // 或者使用onopen, onerror, onclose
````

#### 26.安全的类型检测

typeof的判断并不可靠，比如某些浏览器下typeof 正则返回“function” 

````javascript
typeof /[a-z]/g // chrome下返回object
````

instanceof在多个全局作用域下不能准确判断

````javascript
value instanceof Array // 如果value是在另一个frame下定义的，则返回false
````

#### 27.作用域安全

````javascript
function Person(name, age, job){
    if(this instanceof Person){
        this.name = name;
        this.age = age;
        this.job = job;
    }else{
        return new Person(name, age, job)
    }
}
````

#### 28.惰性载入函数

在某些不同浏览器使用不同函数的情形中，可以使用惰性载入的方式，减少每次调用函数的判断

````javascript
// 原始函数，针对不同浏览器环境有很多判断
function createXHR(){
    if(xx){
        xxx
    }
}
// 方式一 在判断中覆盖原函数，即判断的代码只在第一次执行时执行
function createXHR(){
    if(xxx){
        createXHR = function(){xxx}
    }
}
// 方式二 在声明时直接返回正确的函数，并立即自执行此函数
(function createXHR(){
    if(xxx){
        return function(){xxx}
    }
})()
````

#### 29.防篡改对象

a.不可扩展的对象：不能新增属性

````javascript
var person = { name: "Nicholas"};
alert(Object.isExtensible(person)); // true(判断是否可扩展)
Object.preventExtensions(person);
person.age = 29;
alert(person.age); // undefined
alert(Object.isExtensible(person)); // false
````

b.密封对象：不能新增、删除属性和方法

````javascript
var person = { name: "Nicholas"};
Object.seal(person);
person.age = 29; // 操作在严格模式下报错
alert(person.age); // undefined
delete person.name;
alert(person.name); // "Nicholas"
alert(Object.isSealed(person)); // true
````

c.冻结对象：不能新增、删除和修改属性和方法

````javascript
Object.freeze(person);
Object.isFrozen(person); // true
````

#### 30.节流和防抖

#### 31.cookie：document.cookie = "name=123" 相当于在cookie列表中增加一条name=123的信息，而非重写整个cookie列表

#### 32.性能优化

a.避免对象上的属性查找：因为对象的属性查找会查找原型链

> 可以使用数组代替对象的属性查找
>
> 级数过多的属性使用变量缓存

b.优化循环

> 推荐减值迭代
>
> 使用后测试循环：即do-while循环，避免终止条件的计算
>
> 展开循环：减少了条件判断

c.最小化语句数

> 例如声明：var count =5,color="blue",values=[1,2];
>
> 使用数组和对象字面量：var obj = {name:1,age:2}

d.优化DOM操作

> 最小化（减少）现场更新：比如使用fragment的dom片段整个插入代替批量的dom插入
>
> 使用innerHTML
>
> 缓存dom对变量