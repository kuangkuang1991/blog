---
title: vue3.0学习笔记
date: 2021-04-17 22:58
categories: 技术
tags: JS JS基础
toc: true
---

### vue和react的区别

1.vue专有语法：.vue模板文件data、methods、computed计算属性、watch侦听器、setup（3.0新增）

2.vue指令(以v开头)​ v-on,v-click,v-if 等等；

3.vue属性绑定需要指令+字符串的形式，如:class="isActive:activeClass"，react直接使用两层大括号插值

4.react组件的状态必须使用setState，vue直接this.xxx

5.vue和react的生命周期略有不同



### 重点知识点

1.v-is

有些 HTML 元素，诸如 `<ul>`、`<ol>`、`<table>` 和 `<select>`，对于哪些元素可以出现在其内部是有严格限制的。而有些元素，诸如 `<li>`、`<tr>` 和 `<option>`，只能出现在其它某些特定的元素内部。

这会导致我们使用这些有约束条件的元素时遇到一些问题。例如：

```html
<table>
  <blog-post-row></blog-post-row>
</table>
```

这个自定义组件 `<blog-post-row>` 会被作为无效的内容提升到外部，并导致最终渲染结果出错。我们可以使用特殊的 `v-is` 指令作为一个变通的办法：

```html
<table>
  <tr v-is="'blog-post-row'"></tr>
</table>
```

v-is包含的是单引号包含的JavaScript字符串文本，指向一个组件

2.provide / inject

````javascript
//在父组件中：使用原始值
provide: {
    user: 'John Doe'
}
// 或者 使用data 
provide() {
    return {
      todoLength: this.todos.length
    }
}
// 在子组件中
inject: ['user'],
// 则在子组件中即可使用此数据：this.user
````

3.模板引用

````javascript
<template> 
  <div ref="root">This is a root element</div>
</template>
<script>
  import { ref, onMounted } from 'vue'
  export default {
    setup() {
      const root = ref(null)
      onMounted(() => {
        // DOM元素将在初始渲染后分配给ref
        console.log(root.value) // <div>这是根元素</div>
      })
      return {
        root
      }
    }
  }
</script>
````

4.自定义指令

 ````javascript

  directives: {
    myfocus: {
      mounted(el) {
        el.focus();
      },
    },
  },

  // 使用
  <input v-myfocus>

 ````

 作用：选项中derectives下的myfocus与模板中的v-myfocus绑定，声明了v-myfocus的节点，会在mounted生命周期执行编写的事件函数，参数是对应节点

### vue人性化的部分

1.事件修饰符

````html
<!-- 阻止单击事件继续传播 -->
<a @click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a @click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form @submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div @click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div @click.self="doThat">...</div>
````

2.插槽

````javascript
//在父组件中
<todo-button>
  Add todo
</todo-button>

//在子组件中
<button class="btn-primary">
  <slot></slot>
</button>
````

3.非 Prop 的 Attribute直接成为子组件（单个根元素）dom的attribute

````javascript
//在父组件中
<todo-button class='active' />

//在子组件中：根组件是单一dom组件
<button class="btn-primary">
  <slot></slot>
</button>
````

4.:style中自动添加前缀

5.强制增加响应式： reactive, ref，toRef，或强制只读：readonly

````javascript
import {ref, reactive} from 'vue';
export default {
    setup(){
        let num1 = 10;
        let num2 = ref(10);
        let num3 = reactive(100);
        console.log(num1); // 数值类型10
        console.log(num2); // 对象 {value: 10}
        console.log(num3); // 数值类型100
        return {
            num1,
            num2,
            num3
        }
    }
}
// 
````

> ref-创建出来的数据和以前无关(复制)
> toRef-创建出来的数据和以前的有关(引用)
> ref-数据变化会自动更新界面
> toRef-数据变化不会自动更新界面


6.自定义指令

````javascript
const app = Vue.createApp({})
// 注册一个全局自定义指令 `v-focus`
app.directive('focus', {
  // 当被绑定的元素挂载到 DOM 中时……
  mounted(el) {
    // 聚焦元素
    el.focus()
  }
})
// 使用
<input v-focus />
````

7.teleport 在指定位置渲染组件

````javascript
app.component('modal-button', {
  template: `
    <button @click="modalOpen = true">
        Open full screen modal! (With teleport!)
    </button>

    <teleport to="body"> // 关键代码
      <div v-if="modalOpen" class="modal">
        <div>
          I'm a teleported modal! 
          (My parent is "body")
          <button @click="modalOpen = false">
            Close
          </button>
        </div>
      </div>
    </teleport>
  `,
  data() {
    return { 
      modalOpen: false
    }
  }
})
````

8.使用渲染函数替代模板，自定义tagName

````javascript
const { createApp, h } = Vue
const app = createApp({})
app.component('anchored-heading', {
  render() {
    return h(
      'h' + this.level, // 自定义 tag name
      {}, // props/attributes
      this.$slots.default() // array of children 此处表示插槽内容，即模板中的<slot></slot>
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
````

创建虚拟DOM的方法：createNodeDescription方法

````javascript
h(
  // {String | Object | Function | null} tag
  // 一个 HTML 标签名、一个组件、一个异步组件，或者 null。
  // 使用 null 将会渲染一个注释。
  // 必需的。
  'div',

  // {Object} props
  // 与 attribute、prop 和事件相对应的对象。事件比如：onClick，修饰符则使用驼峰拼接：onClickCapture
  // 我们会在模板中使用。
  // 可选的。
  {},

  // {String | Array | Object} children
  // 子 VNodes, 使用 `h()` 构建,
  // 或使用字符串获取 "文本 Vnode" 或者
  // 有插槽的对象。使用插槽：this.$slots.default()，每个插槽都是一个 VNode 数组
  // 可选的。
  [
    'Some text comes first.',
    h('h1', 'A headline'),
    h(MyComponent, {
      someProp: 'foobar'
    })
  ]
)
````