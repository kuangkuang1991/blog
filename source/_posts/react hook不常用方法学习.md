---
title: react hook不常用方法学习
date: 2021-04-30 20:58
categories: 技术
tags: JS JS基础 React
toc: true
---

### useReducer

参考文章：[【React的作弊模式】理解useReducer的优势和高级用法](https://www.58h.com.cn/a/%E3%80%90ReactDeZuoBiMoShi%E3%80%91LiJieUseReducerDeYouShiHeGaoJiYongFa.html)

基础用法：本质上就是将多个useState合并处理。将分开的setData改用dispatch传递到自定义的reducer函数中，使用if判断更新到合并的state对象中

例如：

````javascript
//使用useState
const [data, setData] = useState('');
const [address, changeAddress] = useState('初始地址')；

// 使用useReducer
const initialState = {
    data: '',
    address: '初始地址'
}
const reducer = (state, action)=>{
    if(action.type == 'setData'){
        return {...state, data: action.payload};
    }else if(action.type == 'changeAddress'){
        return {...state, address: action.payload};
    }else {
        throw new Error();
    }
}

const [state, dispatch] = useReducer(initialState, reducer);

//修改data或addresss
dispatch({type: 'setData', payload: '新data'})
````

当然，最适合的使用场景是表单中，同时把payload的值改为formData对象，则不必再写多个if else的判断

文章中另有高级用法，待尝试



### useRef

基础用法：

````javascript
import React, { useState, useEffect, useMemo, useRef } from 'react';
export default function App(props){
  const [count, setCount] = useState(0);
  const couterRef = useRef();
  useEffect(() => {
    document.title = `The value is ${count}`;
    console.log(couterRef.current); // 引用dom
  }, [count]);
  return (
    <button ref={couterRef} onClick={() => {setCount(count + 1)}}>Count: {count}</button>
  );
}
````

useRef与createRef的区别，参考： [你不知道的 useRef](https://zhuanlan.zhihu.com/p/105276393)

> useRef 在 react hook 中的作用, 正如官网说的, 它像一个变量, 类似于 this , 它就像一个盒子, 你可以存放任何东西. **createRef 每次渲染都会返回一个新的引用，而 useRef 每次都会返回相同的引用。**

高级用法：“跨渲染周期”保存数据

作为不影响渲染，同时又能缓存数据的变量使用：

````javascript
import React, { useState, useEffect, useMemo, useRef } from 'react';
export default function App(props){
  const [count, setCount] = useState(0);
  const timerID = useRef();
  useEffect(() => {
    timerID.current = setInterval(()=>{
        setCount(count => count + 1);
    }, 1000); 
  }, []);
  useEffect(()=>{
      if(count > 10){
          clearInterval(timerID.current);
      }
  });
  return (
    <button ref={couterRef} onClick={() => {setCount(count + 1)}}>Count: {count}</button>
  );
}
````



### useMemo

需要厘清useMemo和React.memo的区别

> React.memo本质是高阶组件（HOC），与 PureComponent 很相似，但是是专门给 Function Component 提供的。可以传递一个函数参数来判断是否render（类似shouldComponentUpdate ，但逻辑相反）（不传则进行浅比较）

````javascript
function MyComponent(props) {
  /* render using props */
}
function areEqual(prevProps, nextProps) {
  /*
  return true if passing nextProps to render would return
  the same result as passing prevProps to render,
  otherwise return false。返回 true 时，不会触发 render
  */
}
export default React.memo(MyComponent, areEqual);
````

> useMemo用来减少组件内的特定函数方法的重复执行
>
> 如果你傳給 `useMemo` 的 function 裡面運算很繁重，例如可能要 map 一組很大的陣列，這時候可能就很值得用 `useMemo` 把運算結果暫記起來下次用。

````javascript
// Assume this returns an Array of 3000 records
const menuItemRows = useMemo(
    () => thousandsOfMenuItems.map(menuItem => (
        <MenuItemRow key={menuItem.uuid} name={menuItem.name} />
    )),
    [thousandsOfMenuItems]
);
````

如果不传递第二个依赖项，则每次render都会执行

[什麼時候該使用 useMemo 跟 useCallback](https://medium.com/ichef/%E4%BB%80%E9%BA%BC%E6%99%82%E5%80%99%E8%A9%B2%E4%BD%BF%E7%94%A8-usememo-%E8%B7%9F-usecallback-a3c1cd0eb520) 这篇文章对这两个方法有比较准确的描述：

> React 引入新的 Hooks 系統後，有兩個 Hook 可以拿來做快取加速用途，分別是記住 return value 的 `useMemo` 跟記住 function instance 的 `useCallback` 。但是什麼時候該用到這兩組 Hook 來幫你的 React app 做加速呢？
>
> `useMemo` → 遇到複雜耗時計算時使用
>
> `useCallback` → 大部分不用，僅在搭配 PureComponent 等、或是提供多個 `useEffect` 時使用



### useCallback

> useMemo用来缓存函数计算值，useCallback则用于缓存函数本身

````javascript
export default function Callback() {
    const [count, setCount] = useState(1);
    const [val, setVal] = useState('');
 
    const callback = useCallback(() => {
        console.log(count);
    }, [count]);
    set.add(callback);
 
    return <div>
        <h4>{count}</h4>
        <h4>{set.size}</h4>
        <div>
            <button onClick={() => setCount(count + 1)}>+</button>
            <input value={val} onChange={event => setVal(event.target.value)}/>
        </div>
    </div>;
}
````

如上例子，每次修改count，set.size就会+1，这说明useCallback依赖变量count，count变更时会返回新的函数；而val变更时，set.size不会变，说明返回的是缓存的旧版本函数。

推荐阅读：[什么时候使用 useMemo 和 useCallback](https://jancat.github.io/post/2019/translation-usememo-and-usecallback/)

> 最后，我想说，每个抽象(和性能优化)都是有代价的。应用 [AHA 编程原则](https://kentcdodds.com/blog/aha-programming)，直到确实需要抽象或优化时才去做，这样可以避免承担成本而不会获得收益的情况。
>
> 具体来说，`useCallback` 和 `useMemo`的成本是：对于你的同事来说，你使代码更复杂了；你可能在依赖项数组中犯了一个错误，并且你可能通过调用内置的 hook、并防止依赖项和 memoized 值被垃圾收集，而使性能变差。如果你获得了必要的性能收益，那么这些成本都是值得承担的，但**最好先测量一下**。