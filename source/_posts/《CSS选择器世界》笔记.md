---
title: 《CSS选择器世界》笔记
date: 2023-12-03 22:55
categories: 技术
tags: 
- 技术
- 读书笔记
- css
toc: true
---

1. 以下两行语句结果不相等：
document.querySelectorAll("#myId div div").length;
document.querySelector("#myId").querySelectorAll('div div').length;
CSS 选择器是独立于整个页面的
想要实现作用域效果可以这样写：
document.querySelector("#myId").querySelectorAll(':scope div div').length;

2.相邻兄弟选择符(>)会忽略文本节点和注释节点

3.属性值匹配选择器
[attr]匹配包含指定属性的元素
[attr="val"]值完全匹配选择器（引号可省略）
[attr~="val"]单词完全匹配（值可能有多个，但只匹配单个单词，不匹配单词中的字符串）
[attr|="val"]起始片段完全匹配（匹配单词或单词加短横杠）

4.属性字符匹配：
[attr^="val"] 匹配单词开头
[attr$="val"] 匹配单词结尾
[attr*="val"] 匹配单词包含

5.属性匹配可以增加字母i以忽略大小写（全大写，全小写，部分大小写均支持），如[attr*="val" i]

6.:hover配合transition的可以实现鼠标悬浮的延迟显隐效果

7.:focus默认只能匹配特定元素：表单元素，a，textarea，summary等。普通元素想要响应需要设置contenteditable或tabindex属性

8.:focus和:focus-within的区别。前者只响应当前元素聚焦，后者响应当前元素或子元素聚焦；

9.链接伪类设置样式的推荐顺序：:link > :visited > :hover > :active 

10.:target 锚点伪类，可设置url的hssh值对应id的元素的样式，也可以实现简单的折叠收起或选项卡效果（类似:checked也可以）
:target-within则用于实现:target无法实现的包含子元素效果

11.radonly的输入框不能输入，但能被表单提交；disabled的输入框不能输入也不能提交

12.:placeholder-shown匹配placeholder显示时的输入框

13.:default 匹配默认值（比如checked了的）对应的表单元素（设置样式可方便用户知道哪个是默认值，或设置为推荐）

14.其他表单伪类：
有效验证伪类:valid和:invalid；
范围验证伪类:in-range和:out-of-range 
可选性伪类:required和:optinal

15.:empty匹配空标签（有空格和注释都不会匹配）（可用于信息展示时数据为空的统一样式处理，如加短横杠，又或者是显示 no data）

16.:only-child 匹配唯一的子元素（适合动态加载子元素个数不确定的情形）

17.:nth-child() 从前面开始匹配 :nth-last-child()从后面开始匹配
参数可以是odd（奇数）even（偶数）或者类似An+B这样的不定数。适应场景如：斑马线条纹；列表边缘对齐；

18.伪元素级联1（多个匹配同时生效）： li:first-child:nth-last-child(2) 匹配第一个子元素和从后往前数的第二个子元素

19.:nth-of-type() 和 :nth-last-of-type() 

20.伪元素级联2:多个条件同时满足
input:not(:disabled):not(:read-only){}

21.:is()和:not() 相反。:is的参数可以多个，通过逗号分隔；:not的参数只能是一个

22.:fullscreen全屏伪类，匹配全屏的元素

23.:lang(en) 语言伪类