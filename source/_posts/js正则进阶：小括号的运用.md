---
title: js正则进阶：小括号的运用
date: 2020-07-16 22:48
categories: 技术
tags: 
- 技术
- JS
- JS基础
toc: true
---

## (pattern) 单独匹配

> 匹配 pattern 并获取这一匹配。所获取的匹配可以从产生的 Matches 集合得到，在JScript 中则使用 $0…$9 属性。要匹配圆括号字符，请使用 '\(' 或 '\)'。

用于匹配单独的一段字符。举例：

字符串"abcdef123jklmn"，我想要替换123为ghi，那么可以这样写：

```JavaScript
 "abcdef123jklmn".replace(/([a-zA-Z]+)([0-9]+)/, '$1ghi')
```

其中$1等于([a-zA-Z]+)，也等于abcdef。注意$1在单引号内

## (?:pattern)  匹配但不获取结果

> 匹配 pattern 但不获取匹配结果，也就是说这是一个非获取匹配，不进行存储供以后使用。这在使用 "或" 字符 (|) 来组合一个模式的各个部分是很有用。例如， 'industr(?:y|ies) 就是一个比 'industry|industries' 更简略的表达式。

同样用字符串"abcdef123jklmn"，

```JavaScript
"abcdef123jklmn".replace(/(?:[a-zA-Z]+)([0-9]+)/, '$1ghi')
```

结果是"123ghijklmn"也就是说被(?:)包含的部分没有匹配，$1直接指向了第二个括号的内容，也就是([0-9]+)。

我们再用match看看

直接括号包裹：

```JavaScript
"abcdef123jklmn".match(/([a-zA-Z]+)([0-9]+)/)  
```

结果是  ["abcdef123", "abcdef", "123", index: 0, input: "abcdef123jklmn", groups: undefined]

用(?:)包裹：

```JavaScript
 "abcdef123jklmn".match(/(?:[a-zA-Z]+)([0-9]+)/)
```

结果是 ["abcdef123", "123", index: 0, input: "abcdef123jklmn", groups: undefined]

可以看到结果数组第一项是相同的，都是完全匹配（忽略括号匹配）的字符串，后者数组比前者少一个"abcdef"，说明这一串没有匹配

## (?=pattern) 正向肯定预查

> 正向（字符后）肯定预查（look ahead positive assert），在任何匹配pattern的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。例如，"Windows(?=95|98|NT|2000)"能匹配"Windows2000"中的"Windows"，但不能匹配"Windows3.1"中的"Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。

意思是匹配后面包含了pattern的字符串，但是匹配结果不包含pattern对应的字符串。举例：

```JavaScript
 "abcdef123jklmn".match(/([a-zA-Z]+)(?=[0-9]+)/)
```

结果是 ["abcdef", "abcdef", index: 0, input: "abcdef123jklmn", groups: undefined]

也就是(?=)对应应该匹配的123没有包含在匹配结果里

```JavaScript
 "abcdef123jklmn".replace(/([a-zA-Z]+)(?=[0-9]+)/, '000')
```

结果是  "000123jklmn"

## (?<=pattern) 反向肯定预查

> 反向(look behind)肯定预查，与正向肯定预查类似，只是方向相反。例如，"`(?<=95|98|NT|2000)Windows`"能匹配"`2000Windows`"中的"`Windows`"，但不能匹配"`3.1Windows`"中的"`Windows`"。

效果与正向肯定预查类似，只是方向相反。可以这样记忆：正向肯定预查=字符后包含查找，反向肯定预查=字符前包含查找

```JavaScript
 "abcdef123jklmn".match(/(?<=[a-zA-Z]+)([0-9]+)/)
```

结果是  ["123", "123", index: 6, input: "abcdef123jklmn", groups: undefined]

## (?!pattern) 正向否定预查

> 正向否定预查(negative assert)，在任何不匹配pattern的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。例如"Windows(?!95|98|NT|2000)"能匹配"Windows3.1"中的"Windows"，但不能匹配"Windows2000"中的"Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。

效果与正向肯定预查相反。匹配字符串在目标字符串后，但是是不包含的。

```JavaScript
 "abcdef123jklmn".match(/([a-zA-Z]+)(?![0-9]+)/g)
```

结果是 ["abcde", "jklmn"]。这次我们加上了关键字符g，match的结果就直接等于匹配结果，不再包含index、input等值（因为是多次匹配）。同时可以注意到第一个匹配项没有f，正是因为f的后面跟了123，不再匹配标准内。

## (?<!pattern) 反向否定预查

> 反向否定预查，与正向否定预查类似，只是方向相反。例如"`(?<!95|98|NT|2000)Windows`"能匹配"`3.1Windows`"中的"`Windows`"，但不能匹配"`2000Windows`"中的"`Windows`"。

效果与正向否定预查类似，方向相反

```JavaScript
 "abcdef123jklmn".match(/(?<![a-zA-Z]+)([0-9]+)/g)
```

结果是 ["23"]

再用replace试试

```JavaScript
 "abcdef123jklmn".replace(/(?<![a-zA-Z]+)([0-9]+)/g, '000')
```

也就是替换掉前面没有英文字符的数字，结果是 "abcdef1000jklmn"，23被替换掉了

