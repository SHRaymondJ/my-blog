---
sidebar: auto
title: React 学习笔记
date: 2022-02-08 13:03:56
permalink: /pages/99a7b4/
categories: 
  - 学习笔记
tags: 
  - react
---

# React 笔记

## 1. JSX

1. className
2. htmlFor(label 的 for)
3. dangerousehtml={\_\_html:``} (插入 html)

## 2. React 事件为什么要 bind this

因为 React 事件的 this 默认是 undefined
建议在 constructor 的时候 bind，而不是在绑定事件的时候 bind，可优化性能

## 3. event 参数

1. 绑定 react 事件，默认会在最后添加一个参数 event
2. 这个 event 是 SyntheticEvent, 不是原生的 Event
3. event.nativeEvent 是原生的 Event
4. 所有的事件都被挂载到 root 上

## 4. 受控组件

1. 同时将 state 和 setState 绑定 value 和 onChange 事件
2. input, textarea, select 用 value
3. checkbox 和 radio 用 checked

## 5. 父子组件传递参数

无论是参数还是函数，都是通过 props 进行传递

## 6. setState

1. 不可变值
2. 可能是异步
    - 正常使用的时候是**异步**的
    - 放到 setTimout 里是**同步**的
    - 自定义的 dom 操作中也是**同步**的
3. 可能会合并
    - 当传入的是对象的时候会合并
    - 当传入的是函数的时候不会合并
    ```javascript
    setState({
        name: 'interview',
    })
    setState((prevState, props) => {
        return {
            name: prevState.name,
        }
    })
    ```

## 7. 生命周期

### 单组件生命周期

加载时： 1. constructor 2. render 3. React 更新 dom 和 refs 4. componentDidMount
更新时： 1. new props/setState/forceUpdate()
\*\* shouldComponenetUpdate 2. render 3. React 更新 dom 和 refs 4. componentDidUpdate
卸载
componentWillUnmount

### 父子组件生命周期

子组件 mount 或者 update 完成了，父组件再 mount、update

## 8. 非受控组件

-   只设置了 defaultValue 或 defaultChecked，没有设置 value、checked 和事件的组件就称为非受控组件
-   需要用 ref 拿他们的值
-   初始化一个变量为`const ref = React.createRef()`
-   把变量放在组件的 ref 属性上 `ref={ref}`
-   在要用的地方用通过 `ref.current` 拿到对应的 dom

## 9. Portals

-   将子组件渲染到父组件以外的 dom 节点
-   `React.createPortal(children, container)`
-   将组件包裹在这个函数里，第二个参数是这个组件要渲染到的节点

## 10. React Context

-   全局上下文，轻量的状态管理
-   初始化实例 `React.createContext()`
-   用 context.provider 把组件包裹起来，provider 的 value 就是 context 内存储的值、方法
-   使用：
    -   class component：定义 xxxComponent.contextType = xxxContext, render(){ // 里面可以直接用 this.context}
    -   function component: 在 jsx 外包裹 `<xxxContext.consumer>{value => { jsx }}</xxxContext.consumer>`
    -   还有 useContext()

## 11. 异步加载组件

-   import()
-   React.lazy
-   React.suspense
    同步加载组件：`import Input from '../input'`
    异步加载组件： 1. `const Input = React.lazy(() => import('../input'))` 2. 使用的时候可以包裹 `<React.suspense fallback={<div>loading</div>}></React.suspense>`，给未加载完成时添加显示状态

## 12. SCU - shouldComponenetUpdate

可以用来优化 class component

### 原理：

React 只要父组件渲染了，无论子组件有没有发生更改，都要重新渲染
所以 React 专门留了这么一个钩子让开发人员决定是否需要渲染子组件

### 使用：

如果不设置的话，默认都是返回 true，既需要更新
如果要设置，可以在需要优化的子组件上添加, props 和 state 都可以判断

```javascript
shouldComponentUpdatae((nextProps, nextState) => {
    if (nextProps.name === this.props.name) {
        return false
    }
    return true
})
```

不建议在 SCU 中使用全相等，会影响性能，可以用浅拷贝，从数据层优化，数据层级减少一点

## 13. PureComponent

官方默认做了 SCU 优化 component
`class xxxComponent extends PureComponent {}`

## 14. memo

函数组件的 SCU 优化， 第二个参数相当于 shouldComponenetUpdate
`React.memo(()=>{jsx}, (pre, next)=>{})`

## 15. immutable.js

类似深拷贝，但又不是深拷贝

## 16. 高阶组件 HOC

一种设计模式，就是公共资源都抽离出来放到一个组件中，然后用这个组件包装其他组件的方法，入参和返回值都是组件

## 17. Render props

一种设计模式，就是公共资源都抽离出来放到一个组件中，然后通过调用该组件时，给该组件传入一个组件作为参数，以此来渲染

HOC vs Render props
HOC 模式简单。但是层级更多
Render props： 代码简洁

## 18. Redux

-   发布订阅 store state subscribe dispatch action reducer

### 单项数据流概述

view 点击触发 dispatch action -> reducer 返回 new state -> state 改变引起视图更新

### react-redux

provider

```javascript
// provider 提供 store
const store = createStore(reducer)
<provider store={store}>
    <app />
</provider>
```

connect
mapStateToProps
mapDispatchToProps

```javascript
// connect 高阶组件，将state 和 dispatch 注入到组件 props 中
// mapStateToProps, mapDispatchToProps, 可以自定义属性和事件，参数分别使用的state 和 dispatch，放到props中
const mapStateToProps = (state) => {
    return {
        todos: getVisibleTodos(state.todos, state.visibilityFilter),
    }
}
const mapDispatchToProps = (dispatch) => {
    return {
        onTodoClick: (id) => dispatch(toggleTodo(id)),
    }
}
TodoList = connect(mapStateToProps, mapDispatchToProps)(TodoList)
```

### 异步 action

同步 action 传入的是一个对象
异步 action 传入的是一个以 dispatch 为参数的函数:

```javascript
const addTodoAsync = (text) => {
    return (dispatch) => {
        fetch(url).then((res) => {
            dispatch(addTodo(res.text))
        })
    }
}

// 使用异步 action 的前提是需要中间件
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import rootReducer from './reducers/index'

const store = createStore(rootReducer, applyMiddleware(thunk))
// 多个中间件，可以依次传入，会依次执行: applyMiddleware(thunk, logger, ...)
```

常用的中间件有:

-   redux-thunk
-   redux-promise
-   redux-saga

### 中间件

就是对 dispatch 进行改造

## 19. React-router

-   路由模式：
    -   hash HashRouter
    -   h5 history BrowserRouter
-   动态路由: `/product/:id`
-   然后可以通过 `const id = useParams()` 拿到 id
-   `<Router>` 包裹在最外层，`<Switch>` 内包裹所有路由 `<Route>`, `<Route>` 上定义 path 和 component
-   根目录要加 exact
-   path='\*' 通常用作 404
-   懒加载就是 lazy 和 suspense, 跟懒加载组件一样
-   跳转路由
    -   `<Link to="/">`
    -   js 跳转： `useHistory() -> history.push('/')`

## 20. React 原理

### 函数式编程

一种编程范式
纯函数
不可变值

### vdom 和 diff

```javascript
{
    tag: 'p',
    key: '',
    properties: {

    },
    children: [

    ]
}
```

react 的 diff 区别于其他 diff，一般的 diff 算法追求 **“完全“** 和 **”最小“**。一般的 diff 算法会遍历到最小节点，而 react 的 diff 算法只要发现结构不同，直接卸载，不会再遍历子节点。

1. tree diff

    树的层级比较，如果有结构不同的直接删除，重新加载

2. component diff

    组件和组件的比较，如果发现不是同一个组件，就会直接删除，如果是相同组件，就会保留

3. element diff

    元素层级的比较。

    分为三个操作： 插入、移动、删除

    1. 插入： 如果新节点在老集合中不存在，就会执行插入操作
    2. 移动： 如果新节点在老集合中存在，但是位置不同，做移动操作（根据 key）
    3. 删除： 如果新节点在老集合中存在，但是做出了更改不可复用，就做删除操作

### JSX 本质

```javascript
React.createElement('tagName', null, [child1, child2, child3])
React.createElement('tagName', null, child1, child2, child3)
React.createElement(Tag, null, child1, child2, child3, '文本节点')
// h 函数
// 返回 vnode
// patch(elem, vnode) 和 patch(vnode,newVnode) 实现渲染更新
```

### 合成事件

-   合成事件过程
    -   基于事件冒泡，把事件都挂载到 root 上
    -   root 统一生成合成事件 SyntheticEvent
    -   再 dispatchEvent 派发事件给事件处理函数
-   为什么要合成事件机制
    -   更好的兼容性和跨平台
    -   挂在到 root 上，减少内存消耗，避免频繁解绑
    -   方便统一管理

### setState batchUpdate

-   setState 主流程
    -   把 newState 放到 pending 队列
    -   是否处于 batchUpdate
    -   true 的话，把组件放到 dirtyComponents 中
    -   false 的话，遍历 dirtyComponents 中所有组件，调用 updateComponent, 更新 pending state or props
        > setState 是异步还是同步主要是看是否能命中 batchUpdate 机制
        > 判断 isBatchingUpdates
-   react 的事件、生命周期内的都能命中 batchUpdate
-   setTimout, setInterval, 自定义 dom 事件 无法命中 batchUpdate
-   batchUpdate 机制：
    在 React 事件中，最开始的时候，React 会声明一个 isBatchingUpdates = true，等到事件结束，会将他改成 false
-   transaction 事务机制
    React 内部有一个 transaction 机制，在 React 事件或生命周期的开头执行 initial， 在结尾执行 close。 该机制也服务于 batchUpdate 机制

### 组件渲染过程

1. 拿到 props 和 state
2. render() vnode
3. patch(elem, vnode)

### 组件更新过程

1. newState => 放到 dirtyComponents
2. 遍历生成 newVnode
3. patch(vnode, newVnode)

### patch

patch 内分为两个阶段

1. reconciliation 阶段： diff 算法，纯 js 计算
2. commit 阶段：把 diff 结果渲染成 DOM

### react fiber

-   将 reconciliation 阶段细分
-   当浏览器需要渲染的时候，暂停计算等待渲染完成再继续
-   通过 window.requestIdleCallback 监听是否需要渲染

## React 性能优化

1. 渲染列表的时候用 key
2. 自定义事件、dom 事件及时销毁
3. 合理使用异步组件
4. 减少函数 bind this 的次数
5. 合理使用 scu pureComponent memo
6. 合理使用 immutable.js
7. webpack 优化
8. 前端通用的性能优化
9. SSR

## React vs Vue 区别

1. React 用 JSX 拥抱 js， vue 用模板拥抱 html
2. React 是函数式编程(setState({a:100}))，vue 是声明式编程（data.a = 100）
3. React 需要更多自力更生
