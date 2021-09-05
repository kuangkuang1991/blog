---
title: 重读react官方文档
date: 2021-09-05 10:58
categories: 技术
tags: JS JS基础 React
toc: true
---

最近重读了一遍 [react官方文档中文版](https://zh-hans.reactjs.org/docs/forwarding-refs.html)，又得了些读书笔记：

### 1.ref的使用注意事项

- 只能用在dom节点和class组件上，如果要用在function组件，使用React. forwardRef暴露给父组件（ref转发，forward向前的意思）并在useImperativeHandle中定义父组件可调用的子组件方法

  

### 2.<></>插入key

<></>实际上是<React.Fragment>的简写模式，所以当要增加key时，可以这些写

```javascript
{props.items.map(item => (
     // 没有`key`，React 会发出一个关键警告
     <React.Fragment key={item.id}>
        <dt>{item.term}</dt>
        <dd>{item.description}</dd>
     </React.Fragment>
))}
```

key属性是React.Fragment唯一支持的属性



### 3.不要在render中使用HOC（高阶组件）

```javascript
render() {
  // 每次调用 render 函数都会创建一个新的 EnhancedComponent
  // EnhancedComponent1 !== EnhancedComponent2
  const EnhancedComponent = enhance(MyComponent);
  // 这将导致子树每次渲染都会进行卸载，和重新挂载的操作！
  return <EnhancedComponent />;
}
```

类似于在render中重新定义组件的问题都要注意



### 4.如果useState的初始state需要经过复杂计算得到，可以使用setState(func)

```javascript
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});
```

此函数只会在初始渲染时执行一次



### 5.useEffect的执行时间

默认情况下，useEffect包裹的函数，将在每轮渲染结束后执行（一个延迟事件中被调用）；

如果想要在绘制前执行，请使用 useLayoutEffect

return的清除函数，会在执行下一个effect之前



### 6.useEffect的注意事项

- 尽量在useEffect中声明会调用的函数，而不是调用外部函数，因为这样容易以为没有用到某些props或state，而没有声明必要的依赖数组

```javascript
function Example({ someProp }) {
  function doSomething() {
    console.log(someProp);
  }

  useEffect(() => {
    doSomething();
  }, []); // 🔴 这样不安全（它调用的 `doSomething` 函数使用了 `someProp`）
}
```

```javascript
function Example({ someProp }) {
  useEffect(() => {
    function doSomething() {
      console.log(someProp);
    }

    doSomething();
  }, [someProp]); // ✅ 安全（我们的 effect 仅用到了 `someProp`）
}
```

- 处理组件卸载后请求响应回调的setData引发的报错

```javascript
useEffect(() => {
    let ignore = false;
    async function fetchData() {
      const result = await axios('https://hn.algolia.com/api/v1/search?query=' + query);
      if (!ignore) setData(result.data);
    }
    fetchData();
    return () => { ignore = true; }
}, [query]);
```



### 7.使用reducer处理深度的props回调函数传递

```javascript
const TodosDispatch = React.createContext(null);
function TodosApp() {
  // 提示：`dispatch` 不会在重新渲染之间变化
  const [todos, dispatch] = useReducer(todosReducer);
  // 内部组件树里的任何子节点都可以使用 dispatch 函数来向上传递 actions 到 TodosApp
  return (
    <TodosDispatch.Provider value={dispatch}>
      <DeepTree todos={todos} />
    </TodosDispatch.Provider>
  );
}
```



### 8.useRef和createRef的区别

定义区别：useRef相当于this，是一个容器，用于存放任意变量；createRef专用于dom的引用

使用区别：createRef用于class组件获取dom，放在构造函数中只执行一次，如果放在函数组建中会每次render执行

本质区别：createRef 每次渲染都会返回一个新的引用，而 useRef 每次都会返回相同的引用

```javascript
const App = () => {
  const [renderIndex, setRenderIndex] = React.useState(1);
  const refFromUseRef = React.useRef();
  const refFromCreateRef = createRef(); // 每次render重新返回新的引用

  if (!refFromUseRef.current) {
    // 第一次初始化之后，就不再进入判断，所以这个值停留在1
    refFromUseRef.current = renderIndex;
  }
  if (!refFromCreateRef.current) {
    // 每次render都重新进来，所以这个值一直跟随renderIndex更新
    refFromCreateRef.current = renderIndex;
  }

  return (
    <>
      <p>Current render index: {renderIndex}</p>
      <p>
        <b>refFromUseRef</b> value: {refFromUseRef.current}
      </p>
      <p>
        <b>refFromCreateRef</b> value:{refFromCreateRef.current}
      </p>
      <button onClick={() => setRenderIndex(prev => prev + 1)}>
        Cause re-render
      </button>
    </>
  );
};
```



- 使用技巧1：使用useRef显示上一个值：

  ```javascript
  export default function App(){
      const [count, setCount] = useState(0);
      const preCountUseRef = useRef();
      // 为什么保存的是上一个值：因为useEffect是渲染之后执行，而useRef的修改不会触发render
      useEffect(()=>{
          preCountUseRef.current = count;
      }); // 没有第二个参数，则每次render之后都执行
      return (
      	<div>
          	<p>preCount: {preCountUseRef.current}</p>
  			<p>You clicked {count} times</p>
  			<button onClick={()=>setCount(count+1)}>Click me</button>
      	</div>
      )
  }
  ```

- 使用技巧2：封装成自定义hook：

  ```javascript
  const usePrevious = state =>{
      const ref = useRef();
      useEffect(()=>{
          ref.current = state;
      });
      return ref.current;
  }
  
  // 使用自定义hook
  const preCount = usePrevious(count);
  ```