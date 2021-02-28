---
title: 《JavaScript高级程序设计》的100条tips（上）
date: 2021-02-26 22:10
categories: 技术
tags: JS JS基础
toc: true
---

#### 1.后置递增、递减 与 前置递增、递减的最大区别，是递增和递减操作是在包含他们的语句被求值之后才执行：

前置递减：

````javascript
var num1 =2;
var num2 = 20;
var num3 = --num1 + num2; //等于21，（先对num1进行递减后求值）
var num4 = num1 + num2; //等于21
````

后置递减：（后置操作延迟了对值的递增或递减操作）

````javascript
var num1 =2;
var num2 = 20;
var num3 = num1-- + num2; //等于22  （先对num1求值，执行加法，再对num1递减）
var num4 = num1 + num2; //等于21
````

#### 2.所有函数的参数都是按值传递的：

把函数外部的值复制给函数内部的参数，就和把值从一个变量复制到另一个变量一样。

````javascript
// 基本类型
function addTen(num){
    num += 10;
    return num;
}
var count = 20;
var result = addTen(count);
alert(count); //20，没有变化
alert(result); // 30
````

````javascript
// 引用类型
function setName(obj){
    obj.name = 'Nicholas';
}
var person = new Object();
setName(person);
alert(person.name); // 'Nicholas'
````

引用类型的参数看上去是按引用传递的，但实际上确实是按值传递的：

````javascript
function setName(obj){
    obj.name = 'Nicholas';
    obj = new Object();
    obj.name = 'Greg';
}
var person = new Object();
setName(person);
alert(person.name); // 'Nicholas'  person这个变量没有因为重新赋值而改变，说明传递参数传递的是person纸箱的object地址（值）
````

#### 3.JavaScript没有块级作用域

````javascript
if(true){
    var color = 'blue';
}
alert(color); // "blue";
````

````javascript
for(var i=0;i<10;i++){
    doSomething(i);
}
alert(i); // 10
````

#### 4.JavaScript的垃圾收集：

>1.标记清除
>
>2.引用计数

#### 5.数组模拟栈结构和列队结构：

栈结构（后进先出）

> push方法接收任意数量个参数，把他们逐个添加到数组末尾，并返回修改后数组的长度
>
> pop方法则从数组末尾移除最后一项，返回移除的项

列队结构（先进先出）

> push方法在数组末尾插入新值
>
> shift()移除数组的第一个项并返回该项

或者从相反方向模拟列队：

> unshift方法在数组前端添加任意个项并返回新数组的长度
>
> pop方法则从数组末尾移除最后一项，返回移除的项

#### 6.数组排序

默认情况下，sort()方法按升序排列（小值在前，大值在后），原理是调用每个数组 项的toString()方法，然后比较字符串：

````javascript
var values = [0, 1, 5, 10, 15];
values.sort();
alert(values); // 0, 1, 10, 15, 5 (字符串10在5之前)
````

sort()也可以接收一个比较函数，比较函数接收两个参数，如果想让第一个参数在第二个之前，则返回负数：

````javascript
function compare(value1, vlaue2){
    if(value1 < value2){
        return -1;
    }else if(value1 > value2){
        return 1;
    }else{
        return 0;
    }
}
//或者简单相减，用于数值类型 或者valueOf能返回数组类型的数组
function compare(value1, vlaue2){
    return value1 - value2
}

var values = [0, 1, 5, 10, 15];
values.sort(compare);
alert(values); // 0, 1, 5, 10, 15
````

#### 7.正则表达式

````javascript
var re = null, i;

for(i=0;i<10;i++){
    re = /cat/g;
    console.log(re.test("catastrophe")); // 每次返回true （在新的浏览器版本中）
}
console.log("---");
for(i=0;i<10;i++){
    re = new RegExp("cat", "g");
    console.log(re.test("catastrophe")); // 每次返回true
}
````

以上案例说明正则表达式字面量就等同于直接调用RegExp构造函数，每次创建新的实例

关于exec和match的区别，见文章：[javascript正则表达式exec()与match()的区别说明](https://www.cnblogs.com/sunxiaorui/p/4396089.html) 最大区别是：exec是正则表达式的方法（参数是字符串），match是字符串的方法（参数是正则表达式）

#### 8.Function类型

函数名只是指向函数的指针：

````javascript
function sum(num1, num2){
    return num1 + num2;
}
alert(sum(10, 10)); //20

var anotherSum = sum;
sum = null;
alert(anotherSum(10, 10)); //20
````

函数内部属性

> arguments: 类数组对象，包含传入函数的所有参数
>
> arguments.callee: 指向当前函数（严格模式无效）
>
> arguments.callee.caller: 调用当前函数的函数或环境（调用栈？）（严格模式无效）
>
> length: 函数希望接收的命名参数的个数
>
> prototype: 实例方法的真正所在
>
> 

每个函数都包含两个非继承而来的方法：apply()和call()

> apply() 接收两个参数，一个是在其中运行函数的作用域，另一个是参数数组
>
> call() 接收多个参数，第一个参数是作用域，其余的就是其他参数

````javascript
function sum(num1, num2){
    return num1 + num2;
}

function callSum1(num1, num2){
    return sum.apply(this, arguments)
}

function callSum2(num1, num2){
    return sum.call(this, ...arguments); // ...arguments 扩展运算符 表示分别传入（逐一传入，而非一次传入整个数组）
}

console.log("结果：", callSum2(1,2));
````

#### 9.基本包装类型

Object构造函数会像工厂方法一样，根据传入值的类型返回响应基本包装类型的实例：

````javascript
var obj = new Object("some text");
alert(obj instanceof String); // true
````

传入字符就会创建String的实例，传入数值就会得到Number的实例，传入布尔值就会得到Boolean的实例

注意，使用new 调用基本包装类型的构造函数，域直接调用同名的转型函数不一样：

````javascript
var value = "25";
var number = Number(value); //转型函数
alert(typeof number); // "number"

var obj = new Number(value); //构造函数
alert(typeof obj); // "object"
````

类似Boolean类型问题：

````javascript
var falseObject = new Boolean(false);
var result = falseObject && true;
alert(result); // true;

var falseObject = Boolean(false);
var result = falseObject && true;
alert(result); // false;

var falseObject = false;
var result = falseObject && true;
alert(result); // false;
````

#### 10.字符串方法

split()

````javascript
var colorText = "red,blue,green,yellow";
var color1 = colorText.split(","); // ["red", "blue", "green", "yellow"]
var color2 = colorText.split(",", 2); // ["red", "blue"]
var color3 = colorText.split(/[^\,]+/); //匹配任意非逗号的字符 ["", ",", ",", ",", ""]
````

#### 11.内置对象之Global对象（浏览器环境中就是window）

> isNaN()，isFinite()，parseInt()，parseFloat()都是Global的方法

URI编码方法：（有效的URI不能包含某些字符，例如空格，因此需要此方法编码）

````javascript
var uri = "http://www.wrox.com/illegal value.htm#start";

//"http://www.wrox.com/illegal%20value.htm#start"
alert(encodeURI(uri));//一般用于整个URI，不会对本身属于URI的特殊字符编码

//"http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.htm%23start"
alert(encodeURIComponent(uri));//一般用于URI的某一段，会对所有非标准字符编码（此处不应该使用此方法）
````

对应解码：decodeURI() 和 decodeURIComponent() 

#### 12.Math对象的方法

random方法套用公式：

> 值 = Math.floor( Math.random() * 可能值的总数 + 第一个可能的值)

四舍五入方法：

````javascript
// Math.ceil向上舍入
alert(Math.ceil(25.9)); //26
alert(Math.ceil(25.5)); //26
alert(Math.ceil(25.1)); //26

// Math.round标准舍入(即四舍五入)
alert(Math.round(25.9)); //26
alert(Math.round(25.5)); //26
alert(Math.round(25.1)); //25

// Math.floor向下舍入
alert(Math.floor(25.9)); //25
alert(Math.floor(25.5)); //25
alert(Math.floor(25.1)); //25
````

#### 13.对象的两种属性：数据属性和访问器属性

a.数据属性

> [[Configurable]] 能否通过delete删除属性而重新定义属性，能否修改属性的特性（默认值true）
>
> [[Enumerable]] 能否通过for-in 循环返回属性（默认值true）
>
> [[Writable]] 能否修改属性的值（默认值true）
>
> [[Value]] 属性的数据值（默认值undefined）

要修改属性的默认特性，必须使用Object.defineProperty()方法

````javascript
var person = {};
Object.defineProperty(person, "name", {
    writable: false,
    value: "Nicholas"
})

alert(person.name); // "Nicholas"
person.name = "Greg"; //无效，严格模式报错
alert(person.name); // Nicholas
````

注意，将Configurable设置为false之后，将无法再使用Object.defineProperty()配置其属性

b.访问器属性

>  [[Configurable]] 能否通过delete删除属性而重新定义属性，能否修改属性的特性（默认值true）
>
>  [[Enumerable]] 能否通过for-in 循环返回属性（默认值true）
>
>  [[Get]] 在读取属性时调用的函数（默认值undefined）
>
>  [[Set]] 在设置属性时调用的函数（默认值undefined）

````javascript
var person = {
    _year: 2014,
    edition: 1
};
Object.defineProperty(person, "name", {
    get: function(){
        return this._year;
    },
    set: function(newValue){
        if(newValue > 2004){
            this._year = newValue;
            this.edition += newValue - 2004;
        }
    },
})
````

c.定义多个属性：Object.defineProperties()

d.读取属性的特性：Object.getOwnPropertyDescriptor()

#### 14.原型式继承的Object函数

````javascript
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}
````

ECMAScript 5 通过新增Object.create()方法规范了原型式继承。接收两个参数：一个用作新对象原型的对象，和（可选的）一个为新对象定义额外属性的对象。

````javascript
var person = {
    name: "Nicholas",
    friends: ["Shelby", "Count", "Van"]
}

var antherPerson = Object.create(person); // 或者使用前文的object函数
antherPerson.name = "Greg";
antherPerson.friends.push("Rob");

var yetAntherPerson = Object.create(person);
yetAntherPerson.name = "Linda";
yetAntherPerson.friends.push("Barbie");

alert(person.name); // "Nicholas"
alert(person.friends);  // "Shelby,Count,Van,Rob,Barbie"
````

````javascript
var antherPerson = Object.create(person, {
    name: {
        value: "Greg"
    }
})

alert(antherPerson.name); // "Greg"
````

#### 15.作用域链的副作用

````javascript
function createFunctions(){
    var result = new Array();
    for(var i=0;i<10;i++){
        result[i] = function(){
            return i;
        }
    }
    return result;
}
console.log(createFunctions()); // 每个函数都返回10
````

在定义result函数数组时，i看上去是正确的。但当它们要执行时，createFunctions早就执行结束了，此时查找到i=10，因此result函数数组里的函数都返回10。也就是说，result数组函数里的i，是在执行时才去找的。

````javascript
function createFunctions(){
    var result = new Array();
    for(var i=0;i<10;i++){
        result[i] = function(i){
            return i;
        }(i)
    }
    return result;
}
console.log(createFunctions()); // 每个函数都返回10
````

#### 16.闭包的this

````javascript
var name = "The Window";

var object ={
    name: " My Object",
    getNameFunc: function(){
        return function(){
            return this.name;
        }
    }
}

alert(object.getNameFunc()()); // The Window
````

该例子中的匿名函数，没有取到其外部作用域对象object的name。

原因是object.getNameFunc()执行后，匿名函数暴露在全局作用域下，此时在执行此匿名函数，this就指向全局对象window了。

>  匿名函数的this通常指向全局window 

可以这样修改：

````javascript
var name = "The Window";

var object ={
    name: "My Object",
    getNameFunc: function(){
        var that = this;
        return function(){
            return that.name;
        }
    }
}

alert(object.getNameFunc()()); // My Object特殊
````

特殊情况：

````javascript
var name = "The Window";

var object ={
    name: "My Object",
    getName: function(){
        return this.name;
    }
}

object.getName(); // "My Object"
(object.getName)(); // "My Object"
(object.getName = object.getName)(); // "The Window"
````

以上示例的关键在于， 赋值语句是有返回值的，返回值就是**所赋的值** 。因此(object.getName = object.getName)的结果就是原函数getName。然后在当前全局环境执行getName，即得到“The Window”

````javascript
var a;
var b = (a = 1);
console.log(b); // 1
````

#### 17.函数声明和函数表达式

````javascript
function(){
   //do something 
}(); //出错 函数声明后面不能直接跟括号
````

````javascript
(function(){
    //do something 
})(); // 加上小括号之后，函数声明变成了函数表达式，函数表达式后面可以跟括号
````

类似如下

````javascript
(a = function(){alert('test')})(); // 正常执行function，弹出“test”
````

（截止《JavaScript高级程序设计》第192页）