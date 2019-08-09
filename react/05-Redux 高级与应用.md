## 1. React-dev-tool
#### 安装 React开发调试工具

下载地址：链接:https://pan.baidu.com/s/1a-MmXp5EE0WpADqpL5nU8A  密码:ir0m

![image](http://note.youdao.com/yws/res/9090/032F00AF1D2D4AD9B9FF46BE4F16F1D3)



## 2. TodoList 实现

#### 1. 使用create-react-app 创建项目
项目目录结构如下:

<img width="400" src="http://note.youdao.com/yws/res/9098/193028E3732A44A3A34CD64964E2ECED"/>

目录结构说明：
- actions目录存放action文件
- conponents目录
- Header 头部组件，包含标题和输入框
- TodoList组件，任务列表
- constants：action type常量
- containers：容器组件
- reducers存放reducer文件

#### 2. ActionTypes
在constants目录下新建文件ActionTypes.js
- 把动作类型提取出来，单独定义，方便不同的地方引用
- 结构清晰，action种类一目了然
```
export const ADD_TODO = 'ADD_TODO';
export const DELETE_TODO = 'DELETE_TODO';
```

#### 3. ActionCreator
在actions目录下创建todo.js
- addTodo 添加一条任务
- deleteTodo 根据id减少一条任务
```
import * as types from '../constants/ActionTypes';

export function addTodo(text) {
    return { type: types.ADD_TODO, text };
}

export function deleteTodo(id) {
    return { type: types.DELETE_TODO, id };
}
```
#### 4. Reducer
- 在reducer目录下新建 reducer.js
- 导入ActionTypes
```
import { ADD_TODO, DELETE_TODO } from '../constants/ActionTypes';
```
- 初始state
```
const initialState = [{
    text: '开始使用Redux',
    id: 0
}];
```
- 根据action.type 返回state
- 生成id：
```
function getId() {
    return Math.random().toString(16).slice(2)
}
```
完成代码：
```
//导入ActionTypes
import { ADD_TODO, DELETE_TODO } from '../constants/ActionTypes';

// 初始state
const initialState = [{
    text: '开始使用Redux',
    id: 0
}];
// reducer函数，根据action.type,更新state
export default function todos(state = initialState, action) {
    switch (action.type) {
        case ADD_TODO:
            return [{
                id: getId(),
                text: action.text
            }, ...state];  //es6的扩展运算符 ...

        case DELETE_TODO:
            console.log(action.id)
            return state.filter(todo =>
                todo.id !== action.id
            );
        default:
            return state;
    }
}
// 用于生成id
function getId() {
    return Math.random().toString(16).slice(2)
}
```

#### 5. Header组件
在components目录下新建 Header.js

- 使用input添加输入框
- onChange获取输入内容
- 在componentDidMount函数中添加监听键盘事件
```
componentDidMount(){
  //监听键盘事件
  document.addEventListener("keydown",this.handleEnterKey);
}
```
- 当按下回车键调用添加任务函数
```
// 当按下回车键调用添加任务函数
handleEnterKey = (e) => {
  if(e.keyCode === 13){
    let text = this.state.text;
    text = text.trim(); // 去除空格
  
    if(text != ''){
      this.props.addTodo(text);
      this.setState({ text: '' });
    }
  }
}
```
说明：在header组件中，监听键盘事件，在 componentDidMount 时开启监听，componentWillUmount 解除监听

根据keyCode 判断是否按下回车键，如果按下回车，则执行从props传来的addTodo回调，并且把输入框文本 text传过去

完整代码：
```
import React from 'react';
// 引入 Header样式文件
import './Header.css';

export default class Header extends React.Component{

  constructor(props) {
    super(props);
    // 初始化状态。用于保存输入框输入
    this.state = {
      text: ''
    };
  }
  componentDidMount(){
      //监听键盘事件
      document.addEventListener("keydown",this.handleEnterKey);
  }
  componentWillUmount(){
    //取消事件监听
    document.removeEventListener("keydown",this.handleEnterKey);
  }
  // change事件，用于获取键盘输入
  handleChange(e){
    this.setState({ text: e.target.value });
  }
  // 当按下回车键调用添加任务函数
  handleEnterKey = (e) => {
      if(e.keyCode === 13){
        let text = this.state.text;
        text = text.trim(); // 去除空格
      
        if(text !== ''){
          this.props.addTodo(text);
          this.setState({ text: '' });
        }
      }
  }
  render(){
    return(
    <div className="header">
       <h1>TodoList</h1>
        <input type="text" 
          value={this.state.text}
          onChange={this.handleChange.bind(this)}
          />
    </div>)
  }
}
```
#### 6. TodoList
- 在components目录下新建 TodoList.js
- 根据props传来的数据，展示任务列表
- 使用map函数返回li数组
- 添加删除按钮
```
const todos = this.props.todos;
const arr = todos.map((todo,index) =>{
  return <li key={todo.id}>{index}.{todo.text} 
	<span className="delete" onClick={ () => this.handleClick(todo.id) }>删除</span>
   </li>
})
```
完整代码：
```
import React from 'react';
// 导入todolist的样式文件
import './todolist.css';

export default class TodoList extends React.Component{

  constructor(props) {
    super(props);
    // 从props中获取数据
    this.state = {
      todos:this.props.todos || []
    }
    // 给事件函数绑定this对象
    this.handleClick = this.handleClick.bind(this);
  }
  // 删除todo的事件函数
  handleClick(id){
    this.props.deleteTodo(id)
  }
  render(){
    // 使用map函数，把数据数组转成组件数组
    const todos = this.props.todos;
    const arr = todos.map((todo,index) =>{
      return <li key={todo.id}>{index}.{todo.text} <span className="delete" onClick={ () => this.handleClick(todo.id) }>删除</span>
       </li>
    })
   
    return(
      <div>
          <ul>
            {arr}
          </ul>
      </div>
    )
  }
}
```
#### 7. 容器组件
在Container目录下创建App.js。创建容器组件，建立state和dispatch到prosp的映射
![image](http://note.youdao.com/yws/res/9149/D5DAD3206C4847B08F79FEA73A34309A)
完整代码：
```
import React, { Component } from 'react';
import { connect } from 'react-redux';
import Header from '../components/Header';
import TodoList from '../components/TodoList';
import {addTodo,deleteTodo} from '../actions/todos';

// App展示组件，从props中获取数据
class App extends Component {
  render() {
    const { todos, addTodo, deleteTodo} = this.props;
    return (
      <div className="App">
        <Header addTodo={addTodo} />
        <TodoList todos={todos} deleteTodo={deleteTodo} />
      </div>
    );
  }
}

// 映射状态
function mapStateToProps(state) {
  return  {
    todos: state
  };
}

// 映射函数
function mapDispatchToProps(dispatch) {
  return {
    addTodo: text => dispatch(addTodo(text)),
    deleteTodo: id=> dispatch(deleteTodo(id))
  };
}

// 生成容器组件
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(App);
```

#### 8. 注入store
使用Provider 组件把store注入到容器组件
修改index.js：
```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';

import App from  './containers/App'
import { Provider } from 'react-redux'
import store from './store';

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>, 
    document.getElementById('root'));
```


## 3. 中间件的概念

> 思考：如果想在发出action的前后，打印日志，查看action发出前后的状态，如何实现？

对dispatch进行改造：
```

let next = store.dispatch;
store.dispatch = function dispatchAndLog(action) {
  console.log('dispatching', action);
  next(action);
  console.log('next state', store.getState());
}
```
上面代码中，对store.dispatch进行了重定义，在发送 Action 前后添加了打印功能。这就是中间件的雏形。

中间件就是一个函数，对store.dispatch方法进行了改造，在发出 Action 和执行 Reducer 这两步之间，添加了其他功能。




#### 日志中间件 redux-logger

文档：https://www.npmjs.com/package/redux-logger#install

安装：
```
npm i --save redux-logger
```
使用：
```
// 引入 applyMiddleware函数
import {createStore,applyMiddleware} from 'redux';

import reducer from './reducer';
// 引入日志中间件
import logger from 'redux-logger'
// 使用日志中间件
export default createStore(reducer,applyMiddleware(logger));
```

#### applyMiddlewares
applyMiddlewares这个方法到底是干什么的？

它是 Redux 的原生方法，作用是将所有中间件组成一个数组，依次执行。

## 4. 状态初始化
在使用creatStore创建store的时候，可以传入一个初始化的状态对象，例如：
```
// 初始状态
const initialState = {
    counter:123,
    books:[]
}

export default createStore(reducer,initialState,applyMiddleware(thunk,logger));
```
==注意：初始状态要放在中间件前面，一般是作为createStore的第二个参数==

## 5. 异步操作
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

#### Redux-thunk 

1. 安装：
```
npm i redux-thunk --save
```
2. 使用：
```
// 引入redux-thunk中间件
import  thunk  from 'redux-thunk';
export default createStore(reducer,applyMiddleware(thunk,logger));
```

使用了redux-thunk中间件后，可以使dispatch(actionCreator())，不仅接受对象参数，也可以接受函数参数。

3. 创建异步 action
```
//请求笔记列表
export function getBooks() {
    
    return dispatch => {
        
        return axios('https://www.easy-mock.com/mock/5d1c66e9e85bc1461e567f67/api/books')
            .then(res=>{
                console.log(res)
                dispatch({
                    type:"SAVE_BOOKS",
                    books:res.data.collections
                })
            })
            .catch(error =>{})
    }
}
```
getBooks() 是个ActionCreator,它返回了一个函数。函数的参数为dispatch。我们在这个函数中就可以进行异步操作。
异步操作成功之后，在调用dispatch，通知reducer更新状态。

4. reducer
```
const initialState = {
    counter:0,
    books:[]
}
export default (state = initialState,action) =>{

    switch(action.type){
        case "INCREASE" : 
            return {...state,counter:state.counter + action.num};break;
        
        //保存图书数据，这里的action是异步请求成功后发送过来的
        case "SAVE_BOOKS":
            return {...state,books:action.books};break;

        default : return state;
    }
}
```

## 6. Reducer拆分

Reducer 函数负责生成 State。由于整个应用只有一个 State 对象，包含所有数据，对于大型应用来说，这个 State 必然十分庞大，导致 Reducer 函数也十分庞大。

把reducer根据state的属性，拆分出不同的函数，一个函数对state的一个属性，再通过 combineReducers函数，把他们合并成一个大的reducer

- 根据state的属性定义reducer
- 使用combineReducers合并reducer

![image](http://note.youdao.com/yws/res/9222/4A7C4FBBE21B4832B6E9A8FFF75D18B8)

#### 使用combineReducers合并reducer
- state属性名与Reducer同名

```
import { combineReducers } from 'redux';
const reducers = combineReducers({
    abc,
    user
})
```
- state属性名与Reducer不同名
```
import { combineReducers } from 'redux';
const reducers = combineReducers({
    a:abc,
    b:user
})
```

