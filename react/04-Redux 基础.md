`## 1. 状态管理

#### 1.1 什么是MVC？

![image](http://note.youdao.com/yws/res/8558/2E8AF952D8624547944A9F9DC52F2738)

MVC的全名是Model View Controller，是模型(model)－视图(view)－控制器(controller)的缩写，是一种软件设计典范。

V即View视图是指用户看到并与之交互的界面。

M即Model模型是管理数据 ，很多业务逻辑都在模型中完成。在MVC的三个部件中，模型拥有最多的处理任务。

C即Controller控制器是指控制器接受用户的输入并调用模型和视图去完成用户的需求，控制器本身不输出任何东西和做任何处理。它只是接收请求并决定调用哪个模型构件去处理请求，然后再确定用哪个视图来显示返回的数据。

#### 1.2 MVC只是看起来很美

MVC框架的数据流很理想，请求先到Controller, 由Controller调用Model中的数据交给View进行渲染，但是在实际的项目中，又是允许Model和View直接通信的。然后就出现了这样的结果：

![image](http://note.youdao.com/yws/res/8560/36CE022996934AC6850D600AD44ADE16)

#### 1.3 Flux  💎
在2013年，Facebook让React亮相的同时推出了Flux，React的初衷实际上是用来替代jQuery的，Flux实际上就可以用来替代Backbone.js，Ember.js等一系列MVC架构的前端JS框架。

其实Flux在React里的应用就类似于Vue中的Vuex的作用，但是在Vue中，Vue是完整的mvvm框架，而Vuex只是一个全局的插件。

React只是一个MVC中的V(视图层)，只管页面中的渲染，一旦有数据管理的时候，React本身的能力就不足以支撑复杂组件结构的项目，在传统的MVC中，就需要用到Model和Controller。Facebook对于当时世面上的MVC框架并不满意，于是就有了Flux, 但Flux并不是一个MVC框架，他是一种新的思想。

![image](http://note.youdao.com/yws/res/8563/EDD71DCC6C894B1DA0D73B9DA14F9EF1)

* View： 视图层
* ActionCreator（动作创造者）：视图层发出的消息（比如mouseClick）
* Dispatcher（派发器）：用来接收Actions、执行回调函数
* Store（数据层）：用来存放应用的状态，一旦发生变动，就提醒Views要更新页面

#### 1.4 Flux的流程： 💎

1. 组件获取到store中保存的数据挂载在自己的状态上
2. 用户产生了操作，调用actions的方法
3. actions接收到了用户的操作，进行一系列的逻辑代码、异步操作
4. 然后actions会创建出对应的action，action带有标识性的属性
5. actions调用dispatcher的dispatch方法将action传递给dispatcher
6. dispatcher接收到action并根据标识信息判断之后，调用store的更改数据的方法
7. store的方法被调用后，更改状态，并触发自己的某一个事件
8. store更改状态后事件被触发，该事件的处理程序会通知view去获取最新的数据

#### 1.5 Flux的实现
在网络上有多达十余种Flux的实现

https://github.com/voronianski/flux-comparison

常用的Flux框架
* Redux
* Fluxxor
* Reflux
* …

简单说，Flux 是一种架构思想，专门解决软件的结构问题。它跟MVC 架构是同一类东西，但是更加简单和清晰。

Flux存在多种实现（至少15种），在实际开发中，我们使用更加流行的Redux，他也是一种Flux的实现

----
## 2. Redux
<img  width="300" src="http://note.youdao.com/yws/res/8836/4CA6A932E1544DE78B5A5EA9E0A44310"/>

##### 中文文档： https://www.redux.org.cn/
##### 英文文档：https://redux.js.org/introduction/getting-started


#### 2.1 什么是Redux
Redux 是 JavaScript 状态容器， 提供可预测化的状态管理
![image](http://note.youdao.com/yws/res/8859/E5CD3D4AB25B4650B48EE55016DF712B)
* 使用store 统一管理状态
* 组件通过 dispatch 将 state 传入 store
* 组件通过 subscribe 从 store 获取到 state 的改变


使用 Redux和不使用Redux的比较

![image](http://note.youdao.com/yws/res/8863/8C0405C36A6B479BA83454F3BB21B526)


#### 2.2 什么时候使用？ 

> 如果你不知道是否需要 Redux，那就是不需要它
只有遇到 React 实在解决不了的问题，你才需要 Redux

简单说，如果你的UI层非常简单，没有很多互动，Redux 就是不必要的，用了反而增加复杂性。

* 用户的使用方式非常简单
* 用户之间没有协作
* 不需要与服务器大量交互，也没有使用 WebSocket
* 视图层（View）只从单一来源获取数据
需要使用Redux的项目:

用户的使用方式复杂
不同身份的用户有不同的使用方式（比如普通用户和管理员）
多个用户之间可以协作
与服务器大量交互，或者使用了WebSocket
View要从多个来源获取数据
从组件层面考虑，什么样子的需要Redux：

* 某个组件的状态，需要共享
* 某个状态需要在任何地方都可以拿到
* 一个组件需要改变全局状态
* 一个组件需要改变另一个组件的状态

Redux的设计思想：

1. Web 应用是一个状态机，视图与状态是一一对应的。
2. 所有的状态，保存在一个对象里面（唯一数据源）。

==注意：flux、redux都不是必须和react搭配使用的，因为flux和redux是完整的架构，在学习react的时候，只是将react的组件作为redux中的视图层去使用了。==

#### 2.3 Redux 的三大基本原则
* Single Source of Truth(唯一的数据源)
* State is read-only(state 是只读的，只能通过触发action修改state)
* Changes are made with pure function(数据的改变必须通过纯函数完成)

#### 2.4 Store

Redux 应用只有一个单一的 store
- 维持应用的 state
- 提供 getState() 方法获取 state
- 提供 dispatch(action) 方法更新 state
- 通过 subscribe(listener) 注册监听器
- 通过 subscribe(listener) 返回的函数注销监听器
    ```
    let unsubscribe = store.subscribe(handleChange)
    unsubscribe()
    ```
- 使用Redux.createStore（reducer）创建 store

#### 2.5 Action
- 通过动作来修改state的值，是store数据的唯一来源
- Action是一个js对象
    - 必须有一个字段type，来表示我们的动作名称
    - 其他字段为传递的数据
    ```
    {
        type: ADD_TODO',
        index: 5
    }
    ```
Action 是把数据从应用传到 store 的有效载荷。它是 store 数据的唯一来源。
Action 本质上是 JavaScript 普通对象。我们约定，action 内必须使用一个
字符串类型的 type 字段来表示将要执行的动作。

一般来说会通过 store.dispatch() 将 action 传到 store。

#### 2.5 Reducer

* Reducer是一些纯函数
* 接收先前的 state 和 action，并返回新的 state

```
var someReducer = function(state, action) {
  ...
  return state;
}

```

正如之前说的，Redux 不允许应用直接修改 state。它要求使用载体 action 来描述 state 的变化，
通过发送 action 到 store 来改变 state。为了描述 action 如何改变 state tree ，你需要编写 reducers。

Reducer 只是一些纯函数，它接收先前的 state 和 action，并返回新的 state

##### 什么是纯函数？

1、不使用外界网络或数据库调用       
2、返回的值完全取决于其参数的值     
3、调用具有相同的参数集的一个纯函数始终返回相同的值     

总之，一个函数在程序执行的过程中只会根据输入参数给出运算结果，我们把这类函数称为“纯函数”。
纯函数由于不依赖外部变量，使得给定函数输入其返回结果永远不变。

比如整数的加法函数，它接收两个整数值并返回一个整数值，对于给定的两个整数值，它的返回值永远是相同的整数值。


## 3. 第一个Redux程序

1. 新建html页面，并引入redux：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="./redux.min.js"></script>
</head>
<body>
    
</body>
</html>
```
2. 编写界面,添加两个按钮,显示点击次数
```
<div>
    <h1>
        点击: <span id="value">0</span> 次
        <button id="increment">+</button>
        <button id="decrement">-</button>
    </h1>
</div>
```
3. 添加 counter函数 (reducer)

counter是一个纯函数，也就是只要输入的参数不变，则返回值也不变
在counter 函数中，首先判断state是否存在，如果不存在则初始化，返回0
然后使用 switch语句 判断传入的 action 的type，根据不同的action，进行不同的操作，返回不同的数据
```
function counter(state, action) {
    if (typeof state === 'undefined') {
        return 0
    }
    switch (action.type) {
        case 'INCREMENT':
            return state + 1
        case 'DECREMENT':
            return state - 1
        default:
            return state
    }
}

```

4. 使用Redux.createStore(counter)方法，传入 reducer 创建 store
```
var store = Redux.createStore(counter)
```
5. 监听状态变化，并更新页面
```
var valueEl = document.getElementById('value')
function render() {
      console.log('state发生变化，render页面')
      valueEl.innerHTML = store.getState().toString()
}
render()
store.subscribe(render)
```
6. 触发action，改变state
```
document.getElementById('increment')
    .addEventListener('click', function() {
        store.dispatch({
            type: 'INCREMENT'
        })
    })

```

### 学生练习 ✍️



## 4. 自己实现Redux
不使用react，直接使用原生的html/js来写一个简易的的redux,以此来了解下Redux的实现机制。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Redux principle 04</title>
</head>
<body>
  <h1>redux principle</h1>
  <div class="counter">
    <!-- 17. 点击按钮，派发action-->
    <span class="btn" onclick="store.dispatch({type: 'COUNT_DECREMENT', number: 10})">-</span>
    <span class="count" id="count"></span>
    <span class="btn" id="add" onclick="store.dispatch({type: 'COUNT_INCREMENT', number: 10})">+</span>
  </div>
  <script>
    // 1. 定义一个方法，用于集中管理state和dispatch,并且传入reducer，用于更新状态
    const createStore = (reducer) => {
      // 2. 定义一个初始的state
      let state = null
      // 3. getState用于获取状态
      const getState = () => state
      
      // 4. 定义一个监听器，用于管理一些方法
      const listeners = []
      const subscribe = (listener) => listeners.push(listener)

      // 5. 定义一个dispatch方法，让每次有action传入的时候返回reducer执行之后的结果
      const dispatch = (action) => {
        
        // 6. 调用reducer来处理数据
        state = reducer(state, action)
        
        // 7. 让监听器里的所有方法运行
        listeners.forEach(listener => listener())
      }
      //  8. 初始化state
      dispatch({})
      
      // 9. 对外暴露 getState、dispatch和subscribe
      return {
        getState,
        dispatch,
        subscribe
      }
    }
    
    // 11. 定义一个计数器的状态
    const countState = {
      count: 10
    }
    
    // 12. 定一个方法叫changeState，用于处理state的数据，每次都返回一个新的状态
    const changeState = (state, action) => {
      // 如果state是null, 就返回countState
      if (!state) return countState
      switch(action.type) {
        // 处理减
        case 'COUNT_DECREMENT':
          return {
            ...state,
            count: state.count - action.number
          }
        // 处理加        
        case 'COUNT_INCREMENT':
          return {
            ...state,
            count: state.count + action.number
          }
        default:
          return state
      }
    }

    // 13. 创建一个store，传入reducer
    const store = createStore(changeState)
    
    // 14. 定义一个方法用于渲染计数器的dom
    const renderCount = () => {
      const countDom = document.querySelector('#count')
      countDom.innerHTML = store.getState().count
    }
    // 15. 初次渲染数据
    renderCount()
    // 16. 监听，只要有dispatch，renderCount就会自动运行
    store.subscribe(renderCount)
  </script>
</body>
</html>
```

## 5. 总结Redux的流程：

![image](http://note.youdao.com/yws/res/8601/EE5667DDA325412CB530DF1C51DD90E8)

1.store通过reducer创建了初始状态

2.view通过store.getState()获取到了store中保存的state挂载在了自己的状态上

3.用户产生了操作，调用了actions 的方法

4.actions的方法被调用，创建了带有标示性信息的action

5.actions将action通过调用store.dispatch方法发送到了reducer中

6.reducer接收到action并根据标识信息判断之后返回了新的state

7.store的state被reducer更改为新state的时候，store.subscribe方法里的回调函数会执行，此时就可以通知view去重新获取state


## 6. 在 React中使用Redux
![image](http://note.youdao.com/yws/res/8967/B74ACEAE53E8412AB9D463842A3A4664)

#### 6.1 安装
1. 使用 `create-react-app`创建项目
2. 安装Redux  `npm install redux --save`
3. 安装react-redux `npm install react-redux --save`


#### 6.2 容器组件（Smart/Container Components）和展示组件（Dumb/Presentational Components）

Redux 的 React 绑定库是基于 容器组件和展示组件相分离 的开发思想

简单来说，展示组件就是用来展示内容的组件，它的数据唯一来源是props
修改数据也是调用从 props传来的回调函数

容器组件就是专门用来和数据进行交互的，他可以监听 state的变化以及向redux 派发 action来修改数据



|                | 展示组件                   | 容器组件                           |
| -------------: | :------------------------- | :--------------------------------- |
|           作用 | 描述如何展现（骨架、样式） | 描述如何运行（数据获取、状态更新） |
| 直接使用 Redux | 否                         | 是                                 |
|       数据来源 | props                      | 监听 Redux state                   |
|       数据修改 | 从 props 调用回调函数      | 向 Redux 派发 actions              |
|       调用方式 | 手动                       | 通常由 React Redux 生成            |

#### 6.3 react-redux

react-redux提供两个核心的api：

- Provider: 提供store
- connect: 用于连接容器组件和展示组件

##### 1. Provider

根据单一store原则 ，一般只会出现在整个应用程序的最顶层。

##### 2. connect

语法格式为
```
connect(mapStateToProps?, mapDispatchToProps?, mergeProps?, options?)(component)
```
一般来说只会用到前面两个，它的作用是：

- 把`store.getState()`的状态转化为展示组件的`props`
- 把`actionCreators`转化为展示组件props上的方法
>特别强调：

官网上的第二个参数为`mapDispatchToProps`, 实际上就是`actionCreators`

只要上层中有`Provider`组件并且提供了`store`, 那么，子孙级别的任何组件，要想使用`store`里的状态，都可以通过`connect`方法进行连接。如果只是想连接`actionCreators`，可以第一个参数传递为`null`

#### 6.4 代码示例

1. 改造App.js成展示组件

改造App.js成展示组件，用来显示页面按钮和数字，数字显示从 props传过来，通过触发按钮的点击事件，按钮的点击事件触发 从props传过来的回调函数。
```
function App(props) {
  return (
    <div className="App">
        <h1>
            点击: <span id="value">{props.number}</span> 次
            <button onClick={props.increase}> + </button>
            <button onClick={props.decrease}> - </button>
        </h1>
    </div>
  );
}
```

2. 在src目录下新建actions.js    
在actions中，创建了两个函数，在函数内返回 action对象
这样执行这两个函数，就可以得到action对象。这两个函数就是ActionCreator。
```
export const increase = () => {
    return {
        type: 'INCREMENT'
    }
}

export const decrease = () => {
    return {
        type: 'DECREMENT'
    }
}

```
3. 新建 reducer.js文件，创建 reducer函数，内容和 示例一中一样
需要注意的是，要导出 这个函数，以便在其他地方使用。
```
function counter(state, action) {
    if (typeof state === 'undefined') {
        return 0
    }
    switch (action.type) {
        case 'INCREMENT':
            return state + 1
        case 'DECREMENT':
            return state - 1
        default:
            return state
    }
}
export default counter;
```

4. 为App组件添加容器组件
教学指导：

现在来创建一些容器组件把这些展示组件和 Redux 关联起来。     
技术上讲，容器组件就是使用 store.subscribe() 从 Redux state 树中读取部分数据，并通过props来把这些数据提供给要渲染的组件。你可以手工来开发容器组件，但建议使用 React Redux 库的 connect()方法来生成，这个方法做了性能优化来避免很多不必要的重复渲染。

使用 connect() 前，需要先定义 mapStateToProps 这个函数来指定如何
把当前 Redux store state 映射到展示组件的 props 中。
以及mapDispatchToProps 函数，把dispatch映射到props上

这样，当点击展示组件上的按钮时，就会触发dispatch(increase()) 方法，于是就派发了一个action。

==注：执行increase()函数，得到一个 action对象==
```
const mapStateToProps = state => {
    return {
        number: state
    }
}
const mapDispatchToProps = dispatch => {
    return {
        increase: () => dispatch(increase()),
        decrease: () => dispatch(decrease())
    }
}
const AppContainer = connect(
    mapStateToProps,
    mapDispatchToProps
)(App)
```

5. 整合起来
修改index.js文件
```
import React from 'react';
import ReactDOM from 'react-dom';

import AppContainer from  './container'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import counter from './reducer'

let store = createStore(counter)

ReactDOM.render(
    <Provider store={store}>
        <AppContainer />
    </Provider>, 
    document.getElementById('root'));
```

所有容器组件都可以访问 Redux store，所以可以手动监听它。一种方式是把它以 props 的形式传入到所有容器组件中。
但这太麻烦了，因为必须要用 store 把展示组件包裹一层，仅仅是因为恰好在组件树中渲染了一个容器组件。

建议的方式是使用指定的 React Redux 组件 <Provider> 来 魔法般的 让所有容器组件都可以访问 store，而不必显示地传递它。只需要在渲染根组件时使用即可。

## 7. 总结
* 在React中使用Redux，把组件分为容器组件和展示组件
* 展示组件从props中获取数据和回调函数，展示数据和触发回调
* 容器组件用来访问store，监听state变化和派发action
* mapStateToProps 建立状态到props的映射
* mapDispatchToProps 建立dispatch到props的映射
* connect 函数创建容器对象
* Provider 根组件，使所有容器组件都可以访问到store

## 8. 作业

使用Redux完成TodoList，效果如下：
![image](http://note.youdao.com/yws/res/9022/F13DB3D5CFEA412EA068CA24A7BC26EE)

