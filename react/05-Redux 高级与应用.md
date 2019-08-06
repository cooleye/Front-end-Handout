## Redux异步

通常情况下，action只是一个对象，不能包含异步操作，这导致了很多创建action的逻辑只能写在组件中，代码量较多也不便于复用，同时对该部分代码测试的时候也比较困难，组件的业务逻辑也不清晰，使用中间件了之后，可以通过actionCreator异步编写action，这样代码就会拆分到actionCreator中，可维护性大大提高，可以方便于测试、复用，同时actionCreator还集成了异步操作中不同的action派发机制，减少编码过程中的代码量

#### 常见的异步库：

- Redux-thunk 👈
- Redux-saga
- Redux-effects
- Redux-side-effects
- Redux-loop
- Redux-observable
- …

基于Promise的异步库：

- Redux-promise
- Redux-promises
- Redux-simple-promise
- Redux-promise-middleware
- …

#### 容器组件（Smart/Container Components）和展示组件（Dumb/Presentational Components）

|                | 展示组件                   | 容器组件                           |
| -------------: | :------------------------- | :--------------------------------- |
|           作用 | 描述如何展现（骨架、样式） | 描述如何运行（数据获取、状态更新） |
| 直接使用 Redux | 否                         | 是                                 |
|       数据来源 | props                      | 监听 Redux state                   |
|       数据修改 | 从 props 调用回调函数      | 向 Redux 派发 actions              |
|       调用方式 | 手动                       | 通常由 React Redux 生成            |

#### 使用react-redux
可以先结合`context`来手动连接react和redux。

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